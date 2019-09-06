---
title: 22. 简单实现类似Angular的依赖注入功能
date: 2019-09-06 13:32:33
description:
categories:
  - 前端开发
  - Angular
tags:
  - Angular
  - DI
  - Typescript
---


> 依赖注入是一个很重要的设计模式。 它使用得非常广泛，以至于几乎每个人都把它简称为 DI 。
> Angular 有自己的依赖注入框架，离开它，你几乎没办法构建出 Angular 应用。

下面介绍一种简单的（只有100行左右代码）实现类似Angular依赖注入的方式，先看一个例子

<!-- More -->

```typescript
// a.service.ts
@Injectable()
export class AService {

  constructor() {

  }

  public doSomething() {
    console.log('this is AService::doSomething');
  }

}

// b.service.ts
@Injectable()
export class BService {

  constructor(private readonly a: AService) {

  }

  public doSomething() {
    this.a.doSomething();
    console.log('this is BService::doSomething');
  }

}

// some.module.ts
@Module({
  providers: [
    AService,
    BService,
  ],
})
export class SomeModule {

}
```

在上面的例子中，我们创建了两个`Service`，其中`BService`依赖于`AService`，那么`BService`可以在其构造函数中声明其依赖，我们需要一种方法去自动将`AService`的实例注入到`BService`的私有只读变量`a`中，接下来介绍实现的步骤。

首先我们应该对`Typescript`进行配置，使其支持`Javascript`的装饰器（或者说是注解）,下面是我的配置文件：
```json
{
  "compilerOptions": {
    "lib": [
      "dom",
      "es2015"
    ],
    "target": "es5",
    "module": "commonjs",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

其中，`experimentalDecorators`表示**为ES装饰器启用实验支持**，`emitDecoratorMetadata`表示**在源代码中为装饰声明产生类型的元数据**，配置好了这两项之后，我们才能使用`Typescript`的装饰器，接下来我们需要安装依赖`reflect-metadata`，用于读取和设置元数据。

#### @Injectable的实现
`@Injectable`是一个装饰器，它标识被装饰的类是一个`Provider`，它的声明方式如下
```
export function Injectable(): ClassDecorator {
  return (target) => {

  };
}
```

我们在此装饰器中什么都不做，他只起到一个标识的作用。


#### @Module的实现
`@Module`是一个装饰器，它标识被装饰的类是一个`Module`，它的声明方式如下
```
const DI_IMPORTS_SYMBOL = Symbol('di:imports')
const DI_PROVIDERS_SYMBOL = Symbol('di:providers')

export function Module(options: { imports?: Array<any>, providers?: Array<any> }): ClassDecorator {
  return (target) => {
    Reflect.defineMetadata(DI_IMPORTS_SYMBOL, new Set(options.imports || []), target);
    Reflect.defineMetadata(DI_PROVIDERS_SYMBOL, new Set(options.providers || []), target);
  }
}
```

我们使用`Set`来存储一个`Module`作用域中它所声明的`Providers`和它所引入的其他模块。


#### Factory的实现
我们希望达到的目的是，在使用时可以通过`Factory.create(SomeModule)`来获取一个`Module`的实例，然后通过`Module`实例来获取一个`Provider`，例如`Factory.create(SomeModule).get(BService).doSomething()`，此时应该输出
```
Factory.create(SomeModule).get(BService).doSomething();
// this is AService::doSomething
// this is BService::doSomething
```

Talk is cheap. Show me the code:
```
export namespace Factory {

  export function create(module: Type) {
    const imports: Set<Type> = Reflect.getMetadata(DI_IMPORTS_SYMBOL, module);
    const providers: Set<any> = Reflect.getMetadata(DI_PROVIDERS_SYMBOL, module);
    const providersMap = new Map();

    const importModules = Array.from(imports).map((importModule) => {
      let moduleInstance: ModuleInstance = moduleInstances.get(importModule);
      if(!moduleInstance) {
        moduleInstance = create(importModule);
        moduleInstances.set(importModule, moduleInstance);
      }
      return moduleInstance;
    });
    const moduleInstance = new ModuleInstance(importModules, providersMap);

    providers.forEach(provider => {
      createProvider(provider, providers, moduleInstance);
    });
    return moduleInstance;
  }

  function createProvider(provider: any, providers: Set<any>, moduleInstance: ModuleInstance) {
    let providerInstance = moduleInstance.providers.get(provider);

    if(providerInstance) {
      return providerInstance;
    }

    const deps: Array<any> = Reflect.getMetadata('design:paramtypes', provider);
    if(!deps) {
      throw new Error(`No provider named ${ provider.name }, do yout add @Injectable() to this provider?`);
    }

    const args = deps.map(dep => {
      let depInstance = moduleInstance.providers.get(dep);
      if(!depInstance) {
        if(providers.has(dep)) {
          depInstance = createProvider(dep, providers, moduleInstance);
        } else {
          moduleInstance.imports.some(imp => {
            depInstance = createProvider(dep, new Set(), imp);
            return !!depInstance;
          });
        }
      }
      if(!depInstance) {
        throw new Error(`can not found provider ${ dep.name }`);
      }
      return depInstance;
    });
    providerInstance = new provider(...args);
    moduleInstance.providers.set(provider, providerInstance);
    return providerInstance;
  }

  export class ModuleInstance {

    constructor(
        public imports: Array<ModuleInstance>,
        public providers: Map<any, any>) {

    }

    get<T>(provider: Type<T>) {
      let instance: T = this.providers.get(provider);
      if(!instance) {
        this.imports.some(imp => {
          instance = imp.get(provider);
          return !!instance;
        });
      }
      if(!instance) {
        throw new Error(`No provider named: ${ provider.name }`);
      }
      return instance;
    }
  }

}
```

以上就是整个依赖注入的实现了，感兴趣的朋友可以到我的Github上面查看源代码，核心文件就是`lib/di.ts`，地址是
https://github.com/hungtcs/light-di/blob/master/lib/di.ts

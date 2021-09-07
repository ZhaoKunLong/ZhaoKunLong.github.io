---
title: setup-sentry-with-nestjs
date: 2021-09-07 18:15:08
tags: nestjs sentry nestjs-sentry
---

# setup sentry with nestjs-sentry

### nestjs sentry
- [nestjs-sentry](https://github.com/ntegral/nestjs-sentry#getting-started)
- install 
    ```sh
    npm install --save @ntegral/nestjs-sentry @sentry/node @sentry/types @sentry/tracing
    # notice the sentry/types and sentry/tracing also should install
    ```
### import into nestjs
- register in the App Module
  ```ts
  import { Module } from '@nestjs-common';
  import { SentryModule } from '@ntegral/nestjs-sentry';
  import { ConfigModule } from '@ntegral/nestjs-config';
  import { ConfigService } from '@ntegral/nestjs-config';

  @Module({
    imports: [
      SentryModule.forRootAsync({
        imports: [ConfigModule],
        inject: [ConfigService],
        useFactory: async (configService:ConfigService) => ({
          dsn: configService.get<string>('SENTRY_DSN'),
          debug: true | false,
          environment: 'dev' | 'production' | 'some_environment',// process.env.NODE_ENV
          release: 'some_release', | null, // must create a release in sentry.io dashboard
          logLevel: LogLevel.Debug //based on sentry.io loglevel // here is import from sentry/types
        }),
      })
    ]
  })
  export class AppModule {}
  ```
- Client using in controller 
  - [nestjs-sentry-example](https://github.com/ntegral/nestjs-sentry-example)
  - Support inject the Client into any class 
  ```ts
    import { Controller, Get } from '@nestjs/common';
    import { InjectSentry, SentryService } from '@ntegral/nestjs-sentry';
    import { Breadcrumb, Scope, Severity } from '@sentry/node';
    import { AppService } from './app.service';

    @Controller()
    export class AppController {
      constructor(
        // notice here
        @InjectSentry() private readonly client: SentryService,
        //
        private readonly appService: AppService,
      ) {
        const breadcrumb: Breadcrumb = {
          type: 'sample',
          level: Severity.Info,
          category: 'custom',
          event_id: '1',
          message: 'Generic breadcrumb message',
        };
        client.addBreadcrumb(breadcrumb);
        client.debug('App Controller loaded');
        const scope = new Scope();
        scope.setTag('example', 'sampleTag');
        client.captureException(new Error('sample error'), () => scope);
      }
    }
  ```

- Interceptor using in controller
  ```Ts
    @UseInterceptors(new SentryInterceptor())
    @Controller()
    export class AppController {
      }
  ```

- Using in global
  ```Ts
          @Module({
            providers: [
                {
                  provide: APP_INTERCEPTOR,
                  useValue: new SentryInterceptor(),
                },
             ],
           })
  ```
### note the version
- If nestjs is `7.x.x` please using `2.0.9`
- if nestjs is `8.x.x` please using `3.x.x`

### some normal issue:

  - Q1: ConsoleLogger can not resolve: Cause the nestjs version 8 is using the ConsoleLogger replace to ConsoleLogger [github issue desc version change](https://github.com/ntegral/nestjs-sentry/issues/48)
    > Use `2.0.9` will fix it for nestjs `7.x.x`
    >
    > > If your are using nestjs `8.0.0` can use `3.x.x`. will don't have this issue.
  - Q2 `SentryInterceptor` not work for the whole project

    > cause the import is failed:
    >
    > > should use like this
    ```ts
        import { Module } from '@nestjs/common';
        import { APP_INTERCEPTOR } from '@nestjs/core';
        import { SentryInterceptor } from '@ntegral/nestjs-sentry';
           @Module({
            providers: [
                {
                  provide: APP_INTERCEPTOR,
                  useValue: new SentryInterceptor(),
                },
             ],
           })
           export class AppModule {}
    ```

  - About the `useValue` And `useClass` the different is in here [Different providers](https://docs.nestjs.cn/8/fundamentals)

### other source
- [nest-raven](https://github.com/mentos1386/nest-raven)
- [Setup the sentry client in the custom UseInterceptors](https://mantosz.medium.com/use-sentry-with-nestjs-6d255027d3e1)

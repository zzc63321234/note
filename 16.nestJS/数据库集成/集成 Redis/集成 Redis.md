以集成 `redis` 包为例，在定义 `AppModule` 时通过 `useFactory()` 将 `redis client` 作为 `provider` 传入：

~~~typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UserModule } from './user/user.module';
import { User } from './user/entities/user.entity';
import { createClient } from 'redis';

@Module({
  imports: [UserModule],
  controllers: [AppController],
  providers: [
    AppService,
    {
      provide: 'REDIS_CLIENT',
      async useFactory() {
        const client = createClient({
          socket: { host: 'localhost', port: 6379 },
        });
        await client.connect();
        return client;
      },
    },
  ],
})
export class AppModule {}
~~~

之后便可在 `Service` 中注入使用：

~~~typescript
import { Inject, Injectable } from '@nestjs/common';
import { RedisClientType } from 'redis';

@Injectable()
export class AppService {

  @Inject('REDIS_CLIENT')
  private redisClient: RedisClientType;

  async getHello() {
    const value = await this.redisClient.keys('*');
    console.log(value);

    return 'Hello World!';
  }
}
~~~


在 `Nest` 项目中初始化 `prisma`：

~~~bash
$ npm install prisma -D
~~~

~~~bash
$ npx prisma init --datasource-provider mysql
~~~

配置好 `schema`，定义 `model`：

~~~psl
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

model Department {
  id        Int    @id @default(autoincrement())
  name      String  @db.VarChar(20)
  createTime DateTime @default(now())
  updateTime DateTime @updatedAt
  employees     Employee[]
}

model Employee {
  id         Int       @id @default(autoincrement())
  name      String     @db.VarChar(20)
  phone     String     @db.VarChar(30)  

  deaprtmentId Int
  department     Department      @relation(fields: [deaprtmentId], references: [id])
}
~~~

同步给数据库（同时会生成 `Prisma Client`）：

~~~bash
$ npx prisma migrate dev --name init
~~~

创建一个 `Service` 以集成 `PrismaClient`：

~~~bash
$ nest g service prisma --flat --no-spec
~~~

~~~typescript
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {

    // 开启日志
    constructor() {
        super({
            log: [
                {
                    emit: 'stdout',
                    level: 'query'
                }
            ]
        })
    }

    // 在 onModuleInit 中调用 prisma 的 $connect() 方法，如果不显示调用 $connect()，那么 Prisma Client 会在第一次数据库操作时自动连接数据库，这样会导致第一次数据库操作出现延迟
    async onModuleInit() {
        await this.$connect();
    }
}
~~~

之后便可以在模块中注入 `PrismaService` 来使用 `Prisma Client` 的 `API`。

还可以为每个表（`modle`）单独创建 `Service`：

~~~typescript
import { Inject, Injectable } from '@nestjs/common';
import { PrismaService } from './prisma.service';
import { Prisma } from '@prisma/client';

@Injectable()
export class DepartmentService {

    @Inject(PrismaService)
    private prisma: PrismaService;

    async create(data: Prisma.DepartmentCreateInput) {
        return await this.prisma.department.create({
            data,
            select: {
                id: true
            }
        });
    }
}
~~~

~~~typescript
import { Inject, Injectable } from '@nestjs/common';
import { PrismaService } from './prisma.service';
import { Prisma } from '@prisma/client';

@Injectable()
export class EmployeeService {

    @Inject(PrismaService)
    private prisma: PrismaService;

    async create(data: Prisma.EmployeeCreateInput) {
        return await this.prisma.employee.create({
            data,
            select: {
                id: true
            }
        });
    }
}
~~~


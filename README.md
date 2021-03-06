Hangfire.Mongo
==============

[![Build status](https://ci.appveyor.com/api/projects/status/xjr953s29pwwsuq4?svg=true)](https://ci.appveyor.com/project/sergeyzwezdin/hangfire-mongo) [![Nuget version](https://img.shields.io/nuget/v/Hangfire.Mongo.svg)](https://www.nuget.org/packages/Hangfire.Mongo) [![Nuget downloads](https://img.shields.io/nuget/dt/Hangfire.Mongo.svg)](https://www.nuget.org/packages/Hangfire.Mongo) [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/sergun/Hangfire.Mongo/master/LICENSE)


MongoDB support for [Hangfire](http://hangfire.io/) library. By using this library you can store all jobs information in MongoDB.

# Installation

To install Hangfire MongoDB Storage, run the following command in the Nuget Package Manager Console:

```
PM> Install-Package Hangfire.Mongo
```

## Usage ASP.NET

```csharp
GlobalConfiguration.Configuration.UseMongoStorage("mongodb://localhost/ApplicationDatabase");
app.UseHangfireServer();
app.UseHangfireDashboard();
```

## Usage ASP.NET Core

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // Add framework services.
    services.AddHangfire(config =>
    {
        config.UseMongoStorage("mongodb://localhost/ApplicationDatabase");
    });
}
```

## Custom collections prefix

To use custom prefix for collections names specify it on Hangfire setup:

```csharp
public void Configuration(IAppBuilder app)
{
    GlobalConfiguration.Configuration.UseMongoStorage("mongodb://localhost/ApplicationDatabase",
        new MongoStorageOptions { Prefix = "custom" } );

    app.UseHangfireServer();
    app.UseHangfireDashboard();
}
```

## Naming Convention
Hangfire.Mongo will ignore any naming conventions configured by your application.
E.g. if your application use camel casing like this:
```csharp
  var camelCaseConventionPack = new ConventionPack { new CamelCaseElementNameConvention() };
  ConventionRegistry.Register("CamelCase", camelCaseConventionPack, type => true);
```
it will be ignored by Hangfire.Mongo and Pascal Case will be used instead. Of cause only for Hangfire specific collections.

## Migration

We sometimes introduce breaking changes in the schema. For this reason we have introduced migration.
Three migration strategies exists.
- None

  This is the default migration strategy. It will throw an InvalidOperationException never letting you get up and running if there is a schema version mismatch. So it forces you to decide what migration strategy is best for you and at the same time keeps your data safe.
- Drop

  This will simply just drop your existing Hangfire.Mongo database and update the schema version. No fuzz and ready to start from scratch.
  It is the perfect strategy if you e.g. enque all your jobs at startup.
- Migrate

  This will migrate your database from one schema version to the next until the required schema version is reached. Chances are that not all data can be migrated, why some loss of data might occur. Please use with caution and thougoughly test before deploying to production. We are not responsible for data loss.

  NOTE: Only forward migration is supported. If you need to revert to a previous schema version, you need to **manually** drop or restore the previous database.

```csharp
public void Configuration(IAppBuilder app)
{
    var migrationOptions = new MongoMigrationOptions
    {
        Strategy = MongoMigrationStrategy.Migrate,
        BackupStrategy = MongoBackupStrategy.Collections
    };
    var storageOptions = new MongoStorageOptions
    {
        // ...
        MigrationOptions = migrationOptions
    };
    GlobalConfiguration.Configuration.UseMongoStorage("<connection string with database name>", storageOptions);

    app.UseHangfireServer();
    app.UseHangfireDashboard();
}
```

### Migration Backup
By default a backup is made before attempting to migrate.
You can backup Hangfire collections by "cloning" each collection to the same database.
Or you can chose to copy the entire database. Alternatively you can just skip the backup.
The backup can be customized using MongoMigrationOptions.
NOTE: This software is made by humans in our sparetime - we do our best but will not be held responsible for any data loss.

Contributors
------------

- Sergey Zwezdin ([@sergeyzwezdin](https://github.com/sergeyzwezdin))
- Martin Løbger ([@marloe](https://github.com/marloe))
- Jonas Gottschau ([@gottscj](https://github.com/gottscj))

License
-------

Hangfire.Mongo is released under the [MIT License](https://raw.githubusercontent.com/sergun/Hangfire.Mongo/master/LICENSE).

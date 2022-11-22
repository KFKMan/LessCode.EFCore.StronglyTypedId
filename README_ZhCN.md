# LessCode.EFCore.StronglyTypedId

[English version](https://github.com/yangzhongke/LessCode.EFCore.StronglyTypedId/blob/main/README.md)

����source generator���������������Զ�ΪEntity Framework Core�е�ʵ��������ǿ����Id���͡�

ǿ����Id��������guarded keys������ģ��������ƣ�DDD���е���Ҫ�ض���ʹ��ǿ����Id�������߿���ʹ��ר�������������ʶֵ����������������Guid��ͨ�����������档

�Դ�.NET 7��ʼ�� Entity framework core���˶�"guarded keys"������֧�֣�����ٷ��ĵ�[https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew?WT.mc_id=DT-MVP-5004444#improved-value-generation](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew?WT.mc_id=DT-MVP-5004444#improved-value-generation) .

���ݹٷ��ĵ���Ϊ��ʹ��ǿ����Id�������߱����д�ǳ�����Ĵ��룬����PersonId��PersonIdValueConverter�ȡ��������������Զ�������Щ����ġ�

�÷�:

1. Nuget��'Microsoft.EntityFrameworkCore'�Ƽ�����װ��ʵ�������ڵ���Ŀ�С�

```
Install-Package Microsoft.EntityFrameworkCore
```

�������װ'Microsoft.EntityFrameworkCore'����ǿ����Id������Ȼ�ᱻ���ɣ�����valueconverter���벻�ᱻ���ɣ���Ҫ�������Լ���д��Щ�ࡣ

2. ��Nuget��'LessCode.EFCore.StronglyTypedIdGenerator'��װ��ʵ�������ڵ���Ŀ�С�

```
Install-Package LessCode.EFCore.StronglyTypedIdGenerator
```

3. ��Nuget��'LessCode.EFCore.StronglyTypedIdCommons'��װ��ʵ�������ڵ���Ŀ�С�

```
Install-Package LessCode.EFCore.StronglyTypedIdCommons
```

4. ����Ҫʹ��ǿ����Id��ʵ�����ϱ�ע[HasStronglyTypedId]���Զ����ɵ�ǿ����Id���͵������ǡ�ʵ������+Id�������磬ʵ�����������Person���Զ����ɵ�ǿ����Id������������PersonId��

```csharp
[HasStronglyTypedId]
internal class Person
{
	public PersonId Id { get; set; }
	public string Name { get; set; }
}
```
�Զ����ɵ�PersonId�Ĳ��ִ������£�

```csharp
public readonly struct PersonId
{
	public PersonId(long value) => Value = value;
	public long Value { get; }
	//and more ...
}
```

Ĭ�ϱ�ʶ������long�������ͨ��HasStronglyTypedId�Ĺ��캯�����ƶ���ʶ���͡����磬[HasStronglyTypedId(typeof(Guid))]�Ϳ���ָ����ʶ���͡�

���Nuget��'Microsoft.EntityFrameworkCore'����װ����ʵ����Ŀ���ڵ���Ŀ�У�һ�����ֽ�PersonIdValueConverter ������Ҳ�ᱻ���ɡ�

5. ��Nuget��"LessCode.EFCore"��װ�����XXDbContext��Ŀ���ڵ���Ŀ��.

```csharp
Install-Package LessCode.EFCore
```

��OnModelCreating()�����е���ConfigureStronglyTypedId()����ConfigureConventions()�����е���ConfigureStronglyTypedIdConventions()��

```csharp
class TestDbContext: DbContext
{
    public DbSet<Person> Persons { get; set; }
    public DbSet<Dog> Dogs { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        //your code
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        modelBuilder.ConfigureStronglyTypedId();
    }

    protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
    {
        base.ConfigureConventions(configurationBuilder);
        configurationBuilder.ConfigureStronglyTypedIdConventions(this);
    }
}
```

6. ���µ��ò���:

Test1:

```csharp
class Test1
{
    public static void Select1(TestDbContext ctx, DogId id)
    {
        var dog = ctx.Dogs.Find(id);
        if (dog == null)
        {
            Console.WriteLine("No dog found");
        }
        else
        {
            Console.WriteLine($"Dog: {dog.Name}");
        }
    }

    public static void Select1(TestDbContext ctx, PersonId id)
    {
        var dog = ctx.Persons.SingleOrDefault(p => p.Id == id);
        if (dog == null)
        {
            Console.WriteLine("No dog found");
        }
        else
        {
            Console.WriteLine($"Dog: {dog.Name}");
        }
    }
}
```

Program.cs

```csharp
TestDbContext ctx = new TestDbContext();

Console.WriteLine("*******************Insert*****************");
ctx.Persons.Add(new Person { Name = "tom" });
ctx.Dogs.Add(new Dog {Name="wangwang"});
ctx.SaveChanges();

Console.WriteLine("******************SelectById******************");
Test1.Select1(ctx, new DogId(Guid.Parse(xxxxxxx)));
Test1.Select1(ctx, new PersonId(3));
```
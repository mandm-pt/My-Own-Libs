# MyOwn.IoC

Simple inversion of control container

## Run sample with all types registred

The code that is commited should run like the following.

```diff
user@host:/workspace/My-Own-Libs/MyOwn.IoC$ dotnet run
!LOG - New instance created of type <ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
!LOG - New instance created of type <ConsoleInput>
+LOG - Resolved <IInput,ConsoleInput>
!LOG - New instance created of type <ConsoleOutput>
+LOG - Resolved <IOutput,ConsoleOutput>
!LOG - New instance created of type <Calculator>
+LOG - Resolved <ICalculator,Calculator>
Enter 1st integer number:
2
Enter 2nd integer number:
2
Enter operation (+-/*):
*
The result is: 4
Calculator ran out of memory. Shutting down.
```

## Run with missing registrations

Comment the following like ```myOwnIoC.Register<IInput, ConsoleInput>();```
and you should get the output below.

```diff
user@host:/workspace/My-Own-Libs/MyOwn.IoC$ dotnet run
!LOG - New instance created of type <ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
-LOG - Type not registered <IInput>
!LOG - Moving to next ctor.
-LOG - Can't resolve ICalculator
LOG - Terminating...
```

## Run with missing registrations but falling back to other ctor

In the class `Calculator`, add an extra parameter of type bool to the ctor, and add another
ctor that passes false as default to the new parameter. 
```cs
public Calculator(ILogger logger, IInput input, IOutput output)
    : this(logger, input, output, false)
{
}

public Calculator(ILogger logger, IInput input, IOutput output, bool test)
{
    this.logger = logger;
    this.input = input;
    this.output = output;
}
```
You should get the output below.

```diff
user@host:/workspace/My-Own-Libs/MyOwn.IoC$ dotnet run
!LOG - New instance created of type <ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
!LOG - New instance created of type <ConsoleInput>
+LOG - Resolved <IInput,ConsoleInput>
!LOG - New instance created of type <ConsoleOutput>
+LOG - Resolved <IOutput,ConsoleOutput>
-LOG - Type not registered <System.Boolean>
!LOG - Moving to next ctor.
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
+LOG - Resolved Singleton <ILogger,ConsoleLogger>
!LOG - New instance created of type <ConsoleInput>
+LOG - Resolved <IInput,ConsoleInput>
!LOG - New instance created of type <ConsoleOutput>
+LOG - Resolved <IOutput,ConsoleOutput>
!LOG - New instance created of type <Calculator>
+LOG - Resolved <ICalculator,Calculator>
Enter 1st integer number:
8
Enter 2nd integer number:
4
Enter operation (+-/*):
/
The result is: 2
Calculator ran out of memory. Shutting down.
```
---
title: "Architecture-Software Design Patterns"
categories:
  - Architecture
tags:
  - Design Pattern
  - Singleton
  - Strategy
  - Decorator
  - Adapter
  - Command
---

Let's explore some design patterns (c#).

## Singleton

```csharp
class MySingleton {
  private static readonly Lazy<MySingleton> lazy = new Lazy<MySingleton>();
  public static Instance {
    get {
      return lazy.value;
    }
  }
}
```

OR

```csharp
class MySingleton {
  private object lockObj = new object();
  private static MySingleton _mySingleton;

  private Instance {
    get {
    lock(lockObj) {
      if(_mySingleton == null)
        _mySingleton = new MySingleton();
        return _mySingleton;
      }
    }
  }
}
```

## Strategy

```csharp
class Context {
  IStrategy _strategry;
  public Context(IStrategy strategry) {
    _strategy = strategy;
  }

  public void Exec() {
    _strategry.run();
  }
}
```

## Decorator

```csharp
  class Component {}
  class Decorator: Component {
    Component _component;
    public Decorator(Component component) {
      _component = component;
    }
  }
```

## Adapter

```csharp
  class Target {}
  class Adaptee {}
  class Adapter: Target {
    private Adaptee _adaptee;
    public void Exec() {
    // Extra work
    _adaptee.run();
    // Extra work
    }
  }
```

## Command

```csharp
  class Command {
    abstract void Execute();
  }

  class ConcreteCommand: Command {
    void Execute() {
    }
  }

```

## Composite

```csharp
abstract class Component
    {
        protected string Name;

        public Component(string name)
        {
            Name = name;
        }

        public abstract void Add(Component c);
        public abstract void Remove(Component c);
        public abstract void Display(int depth);
    }

    class Composite : Component
    {
        private readonly List<Component> _children = new();

        public Composite(string name)
          : base(name)
        {
        }

        public override void Add(Component component)
        {
            _children.Add(component);
        }

        public override void Remove(Component component)
        {
            _children.Remove(component);
        }

        public override void Display(int depth)
        {
            System.Console.WriteLine($"Level {depth} : {Name}");

            foreach (Component component in _children)
            {
                component.Display(depth + 1);
            }
        }
    }

    class Leaf : Component
    {
        public Leaf(string name)
          : base(name)
        {
        }

        public override void Add(Component c)
        {
            throw new NotSupportedException();
        }

        public override void Remove(Component c)
        {
            throw new NotSupportedException();
        }

        public override void Display(int depth)
        {
            System.Console.WriteLine($"Level {depth} : {Name}");
        }
    }

```

## Mediator

```csharp
  class NotifyMediator: INotifyMediator {
    readonly IEnumerable<INotifier> _notifiers;
    NotifyMediator(IEnumerable<INotifier> notifiers) {
      _notifiers = notifiers;
    }
    public void Notify()
    {
        _notifiers.ToList().ForEach(x => x.Notify());
    }
  }

  class MyController
  {
    readonly INotifyMediator _notifyMediator;

    public MyController(INotifyMediator notifyMediator)
    {
        _notifyMediator = notifyMediator;
    }

    public void Save()
    {
      // ...
        _notifyMediator.Notify();
    }
  }

  interface INotifier
  {
      void Notify();
      bool CanRun();
  }
  class NotifierX : INotifier
  {
      public void Notify()
      {
          // ...
      }
      public bool CanRun()
      {
          return true;
      }
  }


```

## Observer

```csharp
    class Payload {
        public string Message { get; set; }
    }

    class Subject : IObservable<Payload>
    {
        IList<IObserver<Payload>> Observers { get; } = new List<IObserver<Payload>>();

        public IDisposable Subscribe(IObserver<Payload> observer)
        {
            if (!Observers.Contains(observer))
            {
                Observers.Add(observer);
            }
            return new Unsubscriber(Observers, observer);

        }
        public void SendMessage(string message)
        {
            foreach (var observer in Observers)
            {
                observer.OnNext(new Payload { Message = message });
            }
        }
    }

    class Unsubscriber : IDisposable
    {
        private IObserver<Payload> _observer;
        private IList<IObserver<Payload>> _observers;
        public Unsubscriber(IList<IObserver<Payload>> observers, IObserver<Payload> observer)
        {
            this._observers = observers;
            this._observer = observer;
        }

        public void Dispose()
        {
            if (_observer != null && _observers.Contains(_observer))
            {
                _observers.Remove(_observer);
            }
        }
    }

    class Observer : IObserver<Payload>
    {
        public string Message { get; set; }

        public void OnCompleted(){}

        public void OnError(Exception error) {}

        public void OnNext(Payload value)
        {
            Message = value.Message;
        }

        public IDisposable Subscribe(IObservable<Payload> subject)
        {
            return subject.Subscribe(this);
        }
    }

```

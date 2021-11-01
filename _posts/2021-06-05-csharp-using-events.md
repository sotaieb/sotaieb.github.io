---
title: "Csharp-Using Events"
categories:
  - Csharp
tags:
  - Events
---

Let's understund how events works in c#.

## Delegates VS Events

An event is a pair of add/remove methods (like a property with a get and a set).

Assignment and invocation (=) is private to the containing class.
Subscription (-= , +=) is allowed from outside the containing class.

With a public delegate field, anyone can remove other people's (using assignment =) event handlers, raise the event themselves.

The Publisher-Subscriber (pub-sub) pattern is an implementation of event-driven architecture.

In the Observer pattern, the Observers are aware of the Subscribers.
In Publisher/Subscriber, publishers and subscribers don’t need to know each other.

```csharp
public delegate void NotifyHandler();

    public class SampleEventClient
    {
        void Program() {

            // Use case: Multicast Delegates
            NotifyHandler n = () => Console.WriteLine("Server 1 Notified");
            n += () => Console.WriteLine("Server 2 Notified");
            n.Invoke();

            //n = null; //  Assignment and invocation are public = issues

            // Use case: Delegate + Event
            var e = new NotificationEvent();
            e.Notify += () => Console.WriteLine("Server 1 Notified"); // subscription to event is public
            e.Notify += () => Console.WriteLine("Server 2 Notified");
            //e.Notify = null; // compile error, assignment is private to the event containing class.
            //e.Notify?.Invoke(); // compile error, invocation is private to the event containing class.

            e.Raise();


            // Use case: EventHandler
            var eh = new NotificationEvent();

            eh.OnErrorEventHandler += (o, args) => Console.WriteLine($"Log to file: {args.Message}");
            eh.OnErrorEventHandler += (o, args) => Console.WriteLine($"Log to database: {args.Message}");
            //e.OnErrorEventHandler = (o, args) => Console.WriteLine($"Compile error !"); // Compile error


            // Act
            eh.OnError(this, new ErrorEventArgs("Server 1 error"));

        }
    }

    public class NotificationEvent: INotificationEvent
    {
        public event NotifyHandler Notify = null;
        //public event Action<ErrorEventArgs> OnError;

        // Represents the signature: public delegate void EventHandler(object? sender, EventArgs e);
        public event EventHandler<ErrorEventArgs> OnErrorEventHandler = null;

        // Thread safe event
        // readonly object objLock = new object();
        //public event NotifyHandler Notify
        //{
        //
        //    add {
        //      lock (objLock)
        //      {
        //          Notify += value;
        //      }
        //    }
        //    remove
        //    {
        //       lock (objLock)
        //      {
        //          Notify -= value;
        //      }
        //    }
        //}

        public void Raise()
        {
          // Thread safe event
          // Notify handler;
          // lock (objLock)
          // {
          //    handler = Notify;
          // }
          Notify?.Invoke();
        }

        public void OnError(object o, ErrorEventArgs e)
        {
            OnErrorEventHandler?.Invoke(o, e);
        }
    }

    public interface INotificationEvent {
        event NotifyHandler Notify;
        //event Action<ErrorEventArgs> OnError;
        event EventHandler<ErrorEventArgs> OnErrorEventHandler;
    }

    public class ErrorEventArgs : EventArgs
    {
        public string Message { get; }

        public ErrorEventArgs(string message)
        {
            this.Message = message;
        }
    }

```

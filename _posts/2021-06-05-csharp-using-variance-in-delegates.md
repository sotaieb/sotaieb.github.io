---
title: "Csharp-Using Variance in Delegates"
categories:
  - Csharp
tags:
  - Covariance
  - Contravariance
---

Let's understund what is: variance, invariance, covariance, contravariance in c#.

## Covariance and Contravariance

Covariance permits a method to have return type that is more derived than that defined in the delegate.

Contravariance permits a method that has parameter types that are less derived than those in the delegate type.

```csharp
    public delegate void ContravarianceDelegate(Derived arg);
    public delegate Base CovarianceDelegate();

    public class DelegateVariance
    {
        public void Main()
        {
            ContravarianceDelegate contra = Contravariance_Method1;
            contra += Contravariance_Method2;

            contra(new Derived()); // Output From : ["Derived", "Derived"]
            //Action<Derived> contravarianceAction = Contravariance_Method1;
            //contravarianceAction += Contravariance_Method2;

            // contra(new Base()); // Compile error
            // Action<Base> contravarianceAction = Contravariance_Method1;  // Compile error

            CovarianceDelegate co = Covariance_Method1;
            co += Covariance_Method2;
            co(); // Output From: ["Base", "Derived"]

            //Func<Base> covarianceFunc = Covariance_Method1;
            //covarianceFunc += Covariance_Method2;

            //Func<Derived> covarianceFunc = Covariance_Method1; // Compile error
        }

        public static void Contravariance_Method1(Derived arg)
        {
        }
        public static void Contravariance_Method2(Base arg)
        {
        }

        public static Base Covariance_Method1()
        {
            return new Base();
        }

        public static Derived Covariance_Method2()
        {
            return new Derived();
        }
    }

    public class Base
    {
    }
    public class Derived : Base
    {
    }

```

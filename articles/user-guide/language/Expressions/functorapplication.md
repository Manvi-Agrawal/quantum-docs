---
author: bradben
description: Learn how to use functors with callables in Q#.
ms.author: brbenefield
ms.date: 02/01/2021
ms.service: azure-quantum
ms.subservice: qsharp-guide
ms.topic: reference
no-loc: ['Q#', '$$v']
title: Functors in Q#
uid: microsoft.quantum.qsharp.functorapplication
---

# Functor application

Functors are factories that allow you to access particular specialization implementations of a callable. Q# currently supports two functors; the `Adjoint` and the `Controlled`, both of which can be applied to operations that provide the necessary specializations.

The `Controlled` and `Adjoint` functors commute; if `ApplyUnitary` is an operation that supports both functors, then there is no difference between `Controlled Adjoint ApplyUnitary` and `Adjoint Controlled ApplyUnitary`.
Both have the same type and, upon invocation, execute the implementation defined for the `controlled adjoint` [specialization](xref:microsoft.quantum.qsharp.specializationdeclarations#specialization-declarations).

## Adjoint functor

If the operation `ApplyUnitary` defines a unitary transformation *U* of the quantum state, `Adjoint ApplyUnitary` accesses the implementation of *U†*. The `Adjoint` functor is its own inverse, since *(U†)† = U* by definition. For example, `Adjoint Adjoint ApplyUnitary` is the same as `ApplyUnitary`.

The expression `Adjoint ApplyUnitary` is an operation of the same type as `ApplyUnitary`; it has the same argument and return type and supports the same functors. Like any operation, it can be invoked with an argument of suitable type. The following expression applies the [adjoint specialization](xref:microsoft.quantum.qsharp.specializationdeclarations#specialization-declarations) of `ApplyUnitary` to an argument `arg`:

```qsharp
Adjoint ApplyUnitary(arg) 
```

## Controlled functor

For an operation `ApplyUnitary` that defines a unitary transformation *U* of the quantum state, `Controlled ApplyUnitary` accesses the implementation that applies *U* conditional on all qubits in an array of control qubits being in the |1⟩ state.

The expression `Controlled ApplyUnitary` is an operation with the same return type and [operation characteristics](xref:microsoft.quantum.qsharp.operationsandfunctions#operation-characteristics) as `ApplyUnitary`, meaning it supports the same functors.
It takes an argument of type `(Qubit[], <TIn>)`, where `<TIn>` should be replaced with the argument type of `ApplyUnitary`, taking [singleton tuple equivalence](xref:microsoft.quantum.qsharp.singletontupleequivalence#singleton-tuple-equivalence) into account.

| Operation | Argument Type | Controlled Argument Type |
| --- | --- | --- |
| X | `Qubit` | `(Qubit[], Qubit)` |
| SWAP | `(Qubit, Qubit)` | `(Qubit[], (Qubit, Qubit))` |
| ApplyQFT | `LittleEndian` | `(Qubit[], LittleEndian)` |

Concretely, if `cs` contains an array of qubits, `q1` and `q2` are two qubits, and the operation `SWAP` is as defined [here](xref:microsoft.quantum.qsharp.specializationdeclarations#specialization-declarations), then the following expression exchanges the state of `q1` and `q2` if all qubits in `cs` are in the |1⟩ state:

```qsharp
Controlled SWAP(cs, (q1, q2))
```

>[!NOTE]
> Conditionally applying an operation based on the control qubits being in a state other than the |1⟩ state may be achieved by applying the appropriate adjointable transformation to the control qubits before invocation, and applying the inverses after. Conditioning the transformation on all control qubits being in the |0⟩ state, for example, can be achieved by applying the `X` operation before and after. This can be conveniently expressed using a [conjugation](xref:microsoft.quantum.qsharp.conjugations#conjugations). Nonetheless, the verbosity of such a construct may merit additional support for a more compact syntax in the future.


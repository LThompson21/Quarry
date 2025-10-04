### **Quarry Language Outline**
_File: outline.quarry.md_

---

## **Contents**
1. Language Philosophy & Goals
2. Core Data Model
3. Memory & Pointer Semantics
4. Error Handling
5. Generics, Traits, and Polymorphism
6. Runtime Type System (RTTI)
7. Modules & Interface System (.qri)
8. Build System & Toolchain (.qbuild)
9. Compiler Architecture & Pipeline
10. Standard Library (qstl)
11. Interop Layer (C/C++ + LLVM)
12. Future Directions

---

## **1. Language Philosophy & Goals**
- Data-Oriented, Struct-only core.
- Zero hidden behavior: explicit control of memory, lifetime, and safety.
- Compile-time monomorphization for all generics.
- Designed for systems, games, and real-time applications.

---

## **2. Core Data Model**
- `struct` is the sole composite type.
- `impl TypeName { ... }` defines behavior (methods, traits).
- Structural inheritance: `struct Derived extends Base`.
- Explicit visibility: `public:`, `protected:`, `private:`.

---

## **3. Memory & Pointer Semantics**
- Default pointer: `T*` (non-nullable, mutable).
- Nullable pointer: `T?*` (explicitly nullable).
- Immutable pointer: `const T*` or `const T?*`.
- Lifecycle methods *implicit per Type, can be 'overridden'/'overloaded'*:
  - `New() -> T`  (stack)
  - `Alloc() -> T*`  (heap)
  - `TryAlloc() -> T* | OOMError`  (heap)
  - `Del(ptr: T*)`  (cleanup)
- OOM: `Alloc<T>() throws`, `TryAlloc<T>() -> T* | OOMError`.

---

## **4. Error Handling**
- Result-style unions: `T1 | T2`.
- Pattern matching with `switch` or `isinstance`.
- Fatal errors use exceptions with stack traces.

---

## **5. Generics, Traits, and Polymorphism**
- Templates with `requires` (traits).
- Traits define contracts; `impl` satisfies.
- Static dispatch (zero vtable).

---

## **6. Runtime Type System (RTTI)**
- Global `TypeID` derived from full type name + structure hash.
- `isinstance(obj, Type)` and pattern `switch` for safe casting.
- No implicit runtime vtables.

---

## **7. Modules & Interface System (.qri)**
- `module name { ... }` → compilation boundary.
- Auto-generated `.qri` per compiled module: symbol table, layout, TypeIDs.
- `namespace` purely organizational.
- Linking flow: `source → .qri + .obj → link`.

---

## **8. Build System & Toolchain (.qbuild)**
- Declarative root file: `.qbuild`.
- Defines outputs (exe/dll/static).
- Recursive source discovery.
- `include`, `link` keywords.
- `.qri` acts as ABI-verified header for imported modules.
- Source filenames include `.qr`, `.qry`, `.quarry`

---

## **9. Compiler Architecture & Pipeline**
1. **Frontend**
   - Parse `.qry` → AST.
   - Semantic checks, trait satisfaction, TypeID generation.
2. **Middle-end**
   - Monomorphization, template instantiation.
   - IR generation (LLVM IR).
3. **Backend**
   - Optimization, linking, code emission.
4. **Tools**
   - `qbuild` (build orchestrator)
   - `qdoc` (doc extractor)
   - `qri-gen` (interface exporter)

---

## **10. Standard Library (qstl)**
- **Core:** memory, traits, typeid, error.
- **Containers:** Array, Vector, Map.
- **Memory:** UniquePtr, SharedPtr, Arena.
- **System:** FileIO, OS, Threads.
- **Math:** Vec, Mat, Quat (for future engine work).

---

## **11. Interop Layer (C/C++ + LLVM)**
- Clang frontend for C/C++ headers → `.qri`.
- `T*` in C/C++ maps to `T?*` in Quarry.
- Shared ABI via LLVM IR.
- C/C++ libraries imported through generated `.qri` bindings, must be pre-compiled.

---

## **12. Future Directions**
- Reflection & codegen macros.
- Inline assembly / intrinsics.
- Parallel compilation & incremental linking.
- IDE integration (LSP).
- Experimental borrow-checker layer.

---
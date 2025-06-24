
# Custom C++ Modules in Godot

## Overview

Godot allows extending the engine in a modular way. C++ modules enable adding functionality, integrating external libraries and optimizing critical parts of the game.

## Module Structure

```
modules/
--------| summator/
    --------| config.py
    ├── register_types.h
    ├── register_types.cpp
    ├── summator.h
    ├── summator.cpp
    └── SCsub
```

## Main Files

### summator.h

```cpp
#ifndef SUMMATOR_H
#define SUMMATOR_H

#include "core/object/ref_counted.h"

class Summator : public RefCounted {
    GDCLASS(Summator, RefCounted);
    int count;

protected:
    static void _bind_methods();

public:
    void add(int p_value);
    void reset();
    int get_total() const;
    Summator();
};
#endif
```

### summator.cpp

```cpp
#include "summator.h"

void Summator::add(int p_value) { count += p_value; }
void Summator::reset() { count = 0; }
int Summator::get_total() const { return count; }

void Summator::_bind_methods() {
    ClassDB::bind_method(D_METHOD("add", "value"), &Summator::add);
    ClassDB::bind_method(D_METHOD("reset"), &Summator::reset);
    ClassDB::bind_method(D_METHOD("get_total"), &Summator::get_total);
}

Summator::Summator() { count = 0; }
```

### register_types.h

```cpp
#include "modules/register_module_types.h"

void initialize_summator_module(ModuleInitializationLevel p_level);
void uninitialize_summator_module(ModuleInitializationLevel p_level);
```

### register_types.cpp

```cpp
#include "register_types.h"
#include "core/object/class_db.h"
#include "summator.h"

void initialize_summator_module(ModuleInitializationLevel p_level) {
    if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE) return;
    ClassDB::register_class<Summator>();
}

void uninitialize_summator_module(ModuleInitializationLevel p_level) {}
```

### config.py

```python
def can_build(env, platform):
    return True

def configure(env):
    pass
```

## Building

```sh
scons custom_modules=../modules
```

## Tests

```cpp
TEST_CASE("[Modules][Summator] Soma") {
    Ref<Summator> s = memnew(Summator);
    s->add(10);
    s->add(20);
    CHECK(s->get_total() == 30);
}
```

## Documentation

Use `--doctool` to generate:

```sh
bin/godot --doctool .
```

## Conclusion

You can now use `Summator.new()` directly in GDScript.

---
layout: doc_it
title: Bootstrap
previous: Sistema di build
previous_url: build-system
next: Macchina Virtuale
next_url: virtual-machine
---

Il bootstrap è il processo di costruzione graduale delle funzionalità del
sistema, fino a quando non è possibile eseguire codice Ruby. Il processo
è costituito da sette fasi:

  1. VM: La macchina virtuale è in grado di caricare ed eseguire bytecode,
     inviare messaggi (es. ricercare ed eseguire metodi), e tutte le funzioni
     primitive sono disponibili, ma non ancora accessibili come metodi Ruby.

     A questo punto del processo la classe Class deve essere impostata
     manualmente per usare se stessa come classe e Module come superclasse.
     Oltre a Class e Module, vengono create un paio di altre classi di base,
     e in particolare Object, Tuple, LookupTable, e MethodTable.

     Ora che è possibile definire classi, più o meno 35 classi built-in
     vengono inizializzate, vengono creati i simboli per i metodi top level
     (:object_id, :call, :protected, etc), vengono definite le eccezioni di
     base, e vengono registrate le primitive. Infine viene predisposto l'IO.
     Inoltre in questa fase diversi metodi Ruby fondamentali vengono associati
     alle rispettive primitive.

     A questo punto è possibile iniziare a acricare il resto del kernel, che
     è completamente definito in Ruby. E' necessario che ciò avvenga in più
     passi, di pari passo con la costruzione del linguaggio.

  2. alpha: This starts the loading of Ruby code. The ability to open classes
     and modules and define methods exists. The minimum functionality to
     support the following methods is implemented in kernel/alpha.rb:

       attr_reader :sym
       attr_writer :sym
       attr_accessor :sym
       private :sym
       protected :sym
       module_function :sym
       include mod

     Also, it is possible to raise exceptions and cause the running process to
     exit. This stage lays the foundation for the next two stages.

  3. bootstrap: This stage continues to add the minimum functionality to
     support loading platform and common. The primitive functions are added
     for most of the kernel classes.

  4. platform: The FFI (foreign function interface) system is implemented and
     Ruby method interfaces to platform-specific functions are created.  Once
     this is set up, platform specific things such as pointers, file access,
     math, and POSIX commands are attached.

  5. common: The vast majority of the Ruby core library classes are
     implemented. The Ruby core classes are kept as implementation-neutral as
     possible. Also, most of the functionality for Rubinius specific classes
     is added.

  6. delta: Final versions of methods like #attr_reader, etc. are added. Also,
     implementation-specific versions of methods that override the versions
     provided in common are added.

  7. loader: The compiled version of kernel/loader.rb is run.

     The final stage sets up the life cycle of a ruby process. It starts by
     connecting the VM to the system, sets up load paths, and reads
     customization scripts from the home directory. It traps signals, and
     processes command line arguments.

     After that, it either runs the script passed to it from the command line
     or boots up the interactive ruby shell. When that finishes, it runs any
     at_exit blocks that had been registered, finalizes all objects, and
     exits.

## Load Order

The files in the kernel directories bootstrap, platform, common, and delta,
implement the respective bootstrapping stages above. The order in
which these directories are loaded is specified in runtime/index.

When an rbc file is loaded, code at the script level and in class or module
bodies is executed. For instance, when loading

    class SomeClass
      attr_accessor :value
    end

the call to #attr_accessor will be run. This requires that any code called in
script, class, or module bodies be loaded before the file that calls the code.
The kernel/alpha.rb defines most of the code that will be needed at the script
or module level. However, other load order dependencies exist between some of
the platform, common, delta, and compiler files.

These load order dependencies are addressed by the load_order.txt file located
in each of the kernel/\*\* directories. If you modify code that adds a load
order dependency, you must edit the load_order.txt files to place the depended
on file above the file that depends on it. Also, if you add a new file to one
of the kernel directories, you must add the file name to the load_order.txt
file in that directory. These files are copied to the appropriate runtime/\*\*
directories during build. During each of the bootstrap stages above, the VM
loads the files listed in load_order.txt in order.

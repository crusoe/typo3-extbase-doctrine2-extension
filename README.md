# Extbase ORM Doctrine 2

This TYPO3 4.6+ extension completly replaces the Extbase ORM with Doctrine2.

    Note: This extension is in very early stage and might not live up to the "completly" phrase yet.

## Want to Have Features

* 100% implementation of Extbase Persistence API
* Access to Doctrine2 APIs for more powerful queries, more performant access to objects.
* Support for the extbase mapping syntax, so no rewriting of existing code necessary (hopefully).

## Differences in Implementation

* Extbase can map request arrays to objects directly passed to the controller. Changes of these objects should not be flushed if invalid and such.
* Extbase therefore has methods Repository#update
* Do this either with merge or with ChangeTracking => Explicit

## Implementation Details

### Mapping Driver

We need a mapping driver that works with the following assumptions

* Mapping TCA/@var types to the underlying database types of Persistence API
* Add Mapping type for unix timestamps (ieks) to DateTime
* Enable Fields will be implemented using Doctrine Filters

### AbstractDomainObject

All Doctrine entities have to extend `AbstractDomainObject` or `AbstractEntity`.

By TYPO3 convention every table has at least a 'uid' that is mapped by default by these classes.

### Collection Abstraction

Extbase ORM uses the `Tx_Extbase_Persistence_ObjectStorge` as collection for objects. This is not compatible with Doctrine collections that have a very different API. For now this is a problem that cannot be fixed, you have to use the Doctrine collections API.

### Interfaces and Implementations

* `Tx_Extbase_Persistence_ManagerInterface` needs an implementation. This is relevant for userland code (controllers).
* `Tx_Extbase_Persistence_BackendInterface` only necessary for the default manager, which will be replaced.
* `Tx_Extbase_Persistence_QueryInterface` will be swaped and QueryResultInterface, QueryFactoryInterface and all Logical Operators
* `Tx_Extbase_Persistence_RepositoryInterface` is an important class and should mimic the original one as closely as possible to make a switch easy.

## Bootstrapping Extbase

The Extbase ORM has a fatal flaw. It calls "persist all" whenever a plugin is shutdown. This leads to lots of "comparing and checking" overhead. The Flush operation should be called explicitly by developers. That is why the Bootstrap has to be overwritten to disable this functionality: `Tx_Doctrine2_ExtbaseBootstrap` does this.

## Notes

* This extension follows Doctrine coding standards. Bite me :-)
* For tests run "git submodule update --init", then "phpunit" from the main directory. Non-extbase TYPO3 code will be re-implemented as real stubs.


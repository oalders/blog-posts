Dump installed modules with their versions, may not work on older Perls.  Release new version and test it.:

```
before_install:
  - cpanm Module::Version::Loaded
  - perl -MModule::Version::Loaded=versioned_modules -MData::Dumper -e 'print Dumper( { versioned_modules() } )'
```

Spell tests add `aspell` and `aspell-en` otherwise it will fail without much complaint.

Use || true for commands which are ok to fail

find  /home/travis/.cpanm/work | grep build.log | xargs cat


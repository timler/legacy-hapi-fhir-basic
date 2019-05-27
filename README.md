# legacy-hapi-fhir-basic

This project allows you to compile a legacy version of the [HAPI-FHIR](http://hapifhir.io/) jar so that you will be able to support FHIR v1 and v4 in the same project.

## Background

HAPI FHIR dropped support for DSTU1 after HAPI FHIR 2.5 [in 3.0.0](https://github.com/jamesagnew/hapi-fhir/releases/tag/v3.0.0):

> Support for FHIR R4 (current working draft) has been added (in a new module called hapi-fhir-structures-r4) and support for FHIR DSTU1 (hapi-fhir-structures-dstu) has been removed. Removing support for the legacy DSTU1 FHIR version was a difficult decision, but it allows us the opportunitity to clean up the codebase quite a bit, and remove some confusing legacy parts of the API (such as the legacy Atom Bundle class). 

## Technical details

This project imports the two main HAPI FHIR jars from a Maven Repository (either the central one or your local one):
 - hapi-fhir-base:2.5
 - hapi-fhir-structures-dstu:2.5
 
And then runs the [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/) to change the package names.
 - from `ca.uhn.fhir` to `ca.uhn.fhir.legacy`, and
 - from `org.hl7.fhir` to `org.hl7.fhir.legacy`

It also overrides some properties files that contain package names.

There is an option in the pom.xml to create a jar with or without dependencies - the default is with. If you uncomment the code in the `excludes` artifact set, then you will need to manually include all the dependencies. 

## Get started

**Step 1**: Clone this repository

```
$ git clone https://github.com/timler/legacy-hapi-fhir-basic.git
```

**Step 2**: Run the maven build:

```
$ mvn clean install
```

**Step 3**: Remove your project's imports for hapi-fhir-base and hapi-fhir-structures-dstu and replace with the generated legacy hapi-fhir-basic jar. 

Note you can find the jar in your local Maven repository here: `.m2/repository/ca/uhn/hapi/fhir/legacy/hapi-fhir-basic/2.5/hapi-fhir-basic-2.5.jar `

**Step 4**: Do a find and replace in your codebase to replace all references to the old package name with the "new" legacy package name.

Replace:
 - `ca.uhn.fhir` with `ca.uhn.fhir.legacy`, and
 - `org.hl7.fhir` with `org.hl7.fhir.legacy`

It's possible to do this on the command line using the Linux command sed, but this approach isn't recommended because it will be run in the hidden .git folders and will cause issues.

The recommended approach is to use your IDE. In Eclipse you would go to Search -> File -> enter the old package name -> Click the Replace button and follow the prompts.


**Step 5**: Run regression tests on your codebase to ensure everything is still working. Do not skip this step.

You will now be able to use the newer [HAPI FHIR jars](http://hapifhir.io/download.html) in your project without any issues because the class loader will be able to know whether to use the new or old classes because they exist in a different package structure. 
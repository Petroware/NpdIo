# NPD I/O - NPD access library

The [Norwegian Petroleum Directorate (NPD)](http://npd.no)
maintains a comprehensive database of the activities in the North Sea.
This is public real-time information updated on a daily basis and
made public through the [NPD fact pages](http://factpages.npd.no/factpages/).

The NPD database contains information on more than 7500 wells, 4000 surveys,
120 fields, 1000 facilities, 700 companies, 1200 licenses and more.

 ![NPD I/O library](https://petroware.no/images/NpdIoBox.250.png)

NPD I/O is a Java library for accessing this information through a clean and
simple to use Java API. NPD I/O is available for Java (NpdIo.jar).
The library is lightweight (< 100kB) and self-contained; It has no external
dependencies. NPD I/O access data directly through HTTP - no login or
password is required.

NPD I/O web page: https://petroware.no/npdio.html


## Setup

Capture the NPD I/O code to local disk by:

```
$ git clone https://github.com/Petroware/NpdIo.git
```


### Dependencies

NPD I/O has no external dependenies.


### Building NPD I/O

NPD I/O can be built from its root folder by

```
$ make clean
$ make
$ make jar
```

The NPD I/O delivery will be the `./lib/NpdIo.jar` file.

Building with make requires the make module of the tools repository.


### Creating Javadoc

Public Javadoc: https://petroware.no/npdio/javadoc/index.html

Javadoc can be created locally by:

```
$ make javadoc
```

Entry point will be `./docs/index.html`.

Note the `./overview.html` page that becomes part of the Javadoc.

Note also that there is some Javadoc configuration in `./Makefile`. The Javadoc is not
automatically date stamped. The Javadoc date (current month) is specified in the Makefile.


## Programming examples

Below are a few examples on how to access some of the main data types from the NPD database.

Correct URLs for each NPD data type is available through the NPD web page.

```java
   import no.petroware.npdio.field.*;
   import no.petroware.npdio.well.*;
   :

   //
   // Read development NPD wellbores
   //
   String url = "http://factpages.npd.no/ReportServer?/FactPages/TableView/wellbore_development_all" + "&" +
                "rs:Command=Render" + "&" +
                "rc:Toolbar=false" + "&" +
                "rc:Parameters=f" + "&" +
                "rs:Format=CSV" + "&" +
                "Top100=false" + "&" +
                "IpAddress=80.239.106.206" + "&" +
                "CultureCode=en";
   List<NpdWellbore> npdWellbores = NpdWellDataReader.readDevelopmentWellbores(url);

   // Loop over the wellbores and write to stdout
   for (NpdWellbore npdWellbore : npdWellbores)
     System.out.println(npdWellbore);

   :


   //
   // Read NPD fields
   //
   String url = ...
   List<NpdField> npdFields = NpdFieldReader.readFields(url);

   // Read production data for all fields
   String url = ...
   ProductionReader.readProduction(url, npdFields);

   // Loop over the fields and write oil production to stdout
   for (NpdField npdField : fields) {
     Production production = field.getProduction();

     for (Production.Entry productionEntry : production.getEntries()) {
       int year = productionEntry.getYear();
       int month = productionEntry.getMonth();

       double oilProduction = productionEntry.getOil();

       System.out.println("Oil production: " + year + "/" + month + ": " + oilProduction);
     }
   }

   :
```

Note that in an actual client implementation the reading process would better be
done asynchronous in threads. The NPD I/O library is all thread-safe.

NPD I/O includes Java implementations for the NPD data types _wellbore_, _license_,
_field_, _company_, _survey_, _facility_, _discovery_, _business arrangement area_,
_pipeline_ and _stratigraphy_.

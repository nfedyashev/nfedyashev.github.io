---
layout: post
date: 2021-06-19 16:00
title:  "org-babel script go generate SchemaSpy PostgreSQL database documentation"
category:
- tips
tags:
- org-babel
- postgresql
- schemaspy
---

org-babel self-contained snippet that downloads schemaspy and postgresql client to generate [http://schemaspy.org/][SchemaSpy database documentation]

# Version reference sources:
[[https://jdbc.postgresql.org/download.html][jdbc.postgresql]]
[[https://github.com/schemaspy/schemaspy/releases][schemaspy/releases]]

```
#+HEADER: :var db="dbname"
#+HEADER: :var dbusername="postgres"
#+HEADER: :var exportDestination="doc/schema"
#+HEADER: :var schemaspyVersion="6.0.0"
#+HEADER: :var postgresqlVersion="42.2.21"
#+HEADER: :var directoryPrefix="jar"
#+begin_src sh :results output
wget -nc https://github.com/schemaspy/schemaspy/releases/download/v${schemaspyVersion}/schemaspy-${schemaspyVersion}.jar \
     --directory-prefix=${directoryPrefix}
wget -nc https://jdbc.postgresql.org/download/postgresql-${postgresqlVersion}.jar \
     --directory-prefix=${directoryPrefix}

java -jar ${directoryPrefix}/schemaspy-${schemaspyVersion}.jar \
     -t pgsql \
     -dp ${directoryPrefix}/postgresql-${postgresqlVersion}.jar \
     -db ${db} \
     -host 127.0.0.1 \
     -port 5432 \
     -s public \
     -u ${dbusername} \
     -o ${exportDestination}

google-chrome ${exportDestination}/index.html
#+end_src
```

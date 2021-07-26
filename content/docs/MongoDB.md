---
title: "MongoDB Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
## MongoDB Cheatsheet

You can find a full list of all Command in the [Manual](http://docs.mongodb.org/manual/reference/method/)

open the mongoshell

    mongo

show all database and their size

    show dbs;

switch to a database

    use permissions_production;

show all collections

    show collections;

show all datasets in a collection and format the human readable

    db.rentals.find().pretty()

count all datasets, without showing them

    db.rentals.find().count()

Search for a specific value

    db.users.find({"name": /.*m.*/})

or

    db.users.find({"name": /m/})

to delete events

    db.eventlog_events.remove({al:false, bl:false});
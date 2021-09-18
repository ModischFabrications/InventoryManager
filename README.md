# Inventory Manager

A simple inventory management system built on VueJS, Python and PostgreSQL. 

The need for such a system was conceived as early as 2017, but there was no ideal solution. There are a few [existing solutions](#existing-solutions), but none of them fulfill my needs completely. 

This solution was designed with small electronic parts in mind, but I aim to support generic items with it. They might have a few empty properties, but they shouldn't be missing anything. This could include tools, software licenses, laptops and other items that you want to keep track of. 


## Questions

Templates for items? 
- Electronics can have PCB footprints, octopart integration and more. 
- Laptops have serial numbers, OS versions, MACs

Are storages concrete objects or just a path? Names are paths, but they might need a geo-location, identifier, comment, ...
-> Can storages be items? A car has storage, but also a description

## Installation
Install by using Docker Compose.

## Usage
Remote access is best archieved using secure channels like VPNs or a Wireguard tunnel. Exposing your instance to the open world without any safety features is definitely not recommended until someone with ITSec experience has reviewed everything. 

### Hints
"Shipment" or "@Bob" are simple ways to integrate missing items into the current scheme. 

Locations: Use a clean naming solution for your storages, I_RACK-ROW-COLUMN is usually enough. Nesting is handled by this system, don't worry about label depth. 

[Labels](https://electronics.stackexchange.com/a/110513/217104): Don't write the content, but an identifier on the container. This makes unique labels easier and allows for random additions without manual grouping. You won't search for them by location anyways, that's what this system is for. 

## Core principles
(cheap includes time, complexity and monetary cost)

Keeping track of items only works as long as virtual and real stocks are identical. 
Adding digital items needs to be cheaper than adding physical items. 
A IMS therefore needs great accessibility and the ability to manage items as easy as possible. -> Smartphone for mobile access

Every location and item needs to be uniquely identifiable. -> item-IDs can't collide, change or contain location data. Location-IDs should lead to one or more item-IDs. 

Storages are compositions, they should be able to nest as deep as you want. 

Additional properties can always be added as a comment, but strong types are usually preferable. 

Self-hosting needs to be simple, scalable and without additional costs -> AWS, Firebase and similars are forbidden by scope. 

## Features
These can be pretty much copied from PartKeepr and similar electronics solutions, they have nice and extensive lists. No need to reinvent the wheel (except reinventing this similar soluton, ofc).

### Tier 0: Core features
web-accessible full stack solution

Items:
- GUID (generated?)
- Pretty name
- Storage location
- Comments

search fields with text

### Tier 1
Items:
- Picture
- (perma-linked) Datasheet/handbook
- source/manufacturer
- price (per pcs, per x pcs)
- mutual-exclusive category (e.g. actives/connectors/LEDs/hardware/...)
- non-mutual tags (e.g. transistor, SMD, red, ...)

nested locations

On the fly creation of properties. 

Transfers/Movement

container lookup, nested content

users, each with one Inventory

Import/Export in plain formats (blobs might need manual handling)

### Tier 2
Items: 
- identify part by QR-/Barcode (camera or barcode scanners)
- collections (name, comment, group of items with quantities) (e.g. for BOM, orders)
- nested categories (e.g. electronics/passive/connector)
- different units (e.g. pcs, length, weight, ...)
- fuzzy search
- dynamic fields (e.g. capacitance, v_gs, footprint) to search and filter
- event log/history (e.g. P moved from Y to Z by U at T)

Suggestions.

Mobile UI

user management, account creation.

n Inventory per user, selectable; shared tables

## Tier 3: Possible extensions
External integrations: 
- Octopart (seems to be in many other solutions :shrug:)
- generate labels for parts/storages
- scan existing labels for identification and modification
- interface with locators
- Import/Export other systems (see references)
- Import orders from online shops (reichelt, digikey, pollin, ...)

Dashboard

localization (e.g. en, de, ...)

statistics (e.g. price of BOM, changes per user, ...)

## Decisions

### Access

Mobile apps are powerful, but hard to develop and distribute. Websides are way easier to integrate and access, and there is no need for special mobile-only-features. 



### Database
MongoDB and other NoSQLs are flashy and hyped right now, but there is little need for their ability to use flexible formats. They are not that great for normalized data though. 

SQLite is easy to use and great for being self-contained, but can't scale well. 

My ordering of classical SQLs is `PostgreSQL > MariaDB > MySQL >>>> every commercial solution`. 


### Git
How is the project structured?
- A: One large repo for everything
- B: Separate repos for each component
- C: Master repo with submodules
[Discussion](https://softwareengineering.stackexchange.com/questions/161293/choosing-between-single-or-multiple-projects-in-a-git-repository)

A gives a simple installation and entry point and keeps everything in sync automatically. 
The problem is separation of concerns, because active development of singular components is more expensive. 

C offers flexible development and clear decoupling with specific issues and tracking. Shared features, versioning and more are more difficult though. B is a worse version of C. 

Solution A is used for now, future development might split it into A again. 


LFS? Nope, not anymore. GitHub has made the weird decision to sum LFS storage for all repos but count non-LFS storage per repo. This makes non-LFS simpler to manage and extend without blocking quotas for other projects. 

## References

### Implementation
https://www.freecodecamp.org/news/making-an-awesome-inventory-management-application-in-php-and-mysql-from-start-to-finish-90bc5996680a/


### Existing solutions
Adapting an existing solution is a big commitment, so I wanted to make sure that it's worth it. 
Similar systems usually integrate into ERPs and track IT resources, business transactions or cash flow, but these features have little relevance for me. 

Mostly free and open source implementations were regarded, because I want something than can be operated individually and without artificial limits, without any company being able to introduce unwanted changes. This includes, but is not limited to, pricing, features, sharing, privacy and integrations. 

Rolodex: Traditional offline solution, dead simple. It's how pre-technology people handled that stuff, but I'm too young to not look at my smartphone all day. 

Spreadsheets (Google Sheets, Excel) are a golden hammer, but limited, local-only and difficult to adjust to specific use cases. 

[Notion IMS Template](https://www.redgregory.com/notion/2020/6/14/notion-template-inventory-management-system): 
Easy access and installation, but limited to basic functionality and integrations. Also limited by it's pricing plan. 

[**PartKeepr**](https://github.com/partkeepr/PartKeepr): 
Fitting, but [unclear development state](https://github.com/partkeepr/PartKeepr/issues/1059). 
It supports most features I needed and many more, which is why I evaluated it extensively. 
It's ugly look and huge complexity seems overwhelming at first, but Partkeepr is probably the best thing for FOSS inventory management. 
It can also be extended and customized and offers open APIs, which makes extensions easy. 

[*Mventory*](https://github.com/MakeMonmouth/mventory): Similar motivation, clean API solution with very little frontend. 

[**Part-DB**](https://github.com/Part-DB/Part-DB-symfony): fitting features, but beta and weird rendering on desktop. 

[*InvenTree*](https://github.com/inventree/InvenTree): No demo, but seems to be alive and documented well. 

[PartsBox](https://partsbox.com/): 
Looks to be a professional, [more extensive version of PartKeepr](https://electronics.stackexchange.com/a/112785/217104), but it's also fully commercial. 
While I understand the need for funding and appreciate the free tier I won't pay 50€/month just to allow someone else to look at my parts. 
It's also a closed system without means for extension, which severly limits my ability to adjust it to my future needs. 

[Parts-in-Place](https://partsinplace.com/): Commercial, artificial limits for free users.

[Bomist](https://bomist.com/pricing): Commercial, artificial limits for free users.

[zParts](http://zparts.sourceforge.net/): Local only and probably dead. 

[Personal Inventory](https://github.com/matt-schwartz/personal-inventory): Simple and similar, but seems small and dead. 

[Inventory](https://github.com/mauricecalhoun/inventory/): No demo, no docs, no test.

[Pantry-for-Good](https://github.com/freeCodeCamp/pantry-for-good): Dead

[Mini-Inventory-and-Sales-Management-System](https://github.com/amirsanni/Mini-Inventory-and-Sales-Management-System): Incomplete, no docs, kind of dead. 

[Storaji](https://github.com/IndomaximTechID/storaji): Dead


Note: PHP based systems are surprisingly common




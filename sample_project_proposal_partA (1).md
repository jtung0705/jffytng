# Wedding Nuzlocke
**Pokemon playthroughs** with extra rules.


Name: Jeffery Tung

### Step I. Database Requirements and Planning

#### 1. Purpose and Goals
A Nuzlocke is a fan created challenge within Pokemon games where the player follows these main principles.
- The player **MUST** use the first pokemon they meet at the location. 
- A pokemon **MUST** be released when it faints.
- All pokemon **MUST** be nicknamed. 

The run is lost when all 6 of your Pokemon faint in one battle, even if other available Pokemon are in storage.



The purpose of this database is to store the content and result of each Nuzlocke challenge run. For this database, Pokemon Emerald is our choice since it is the most popular to complete this challenge. This database serves to optimize a playthrough and help you through common mistakes. This can help you understand which bosses can be most difficult or which Pokemon may survive the longest towards the end. Additionally, for this 'Wedding' take on the Nuzlocke challenge, Pokemon will be paired together.

<!-- can die from poison in gen3 -->
#### 1.1 Glossary 


| Term | Meaning |
|---|---|
|Run| A attempt of the game.|
|Location| A place a Pokemon can be found, whether through a trade, route, or cave.|
|Encounter|The first Pokemon met at said location.|
|Dead| A pokemon that no longer has any health points (HP) and cannot be used again.|
|Boss| A required battle in the game.This can be applicable to your rival in the game, the champion, or the evil team member.|
|Boss Ace Level|The highest level of the next bosses' Pokemon.|
|Champion|The last boss you fight before the credits roll. Defeating the Champion ensures a win. |
|Wipe| A run that ends in a loss. When all of your party members have fainted.|
|Abandoned|A run the player stopped playing before winning or wiping. It ends with no win and no wipe recorded.|


### 2. Business Rules
#### 2.1 Identity rules

| ID | Business Rule | 
|---|---|
| 01 | Each caught Pokemon can have only one nickname, and no two caught Pokemon can share a name in the same run.  |
| 02 | Each species is also identified through a National Pokedex Number starting from #1 - #386. |
| 03 | Every location has one location ID and one name. There is no location that can share a name with another. | 
| 04 | Every boss encounter is identified by a boss number. 
| 05 | Each run is identified by exactly one run ID.
| 06 | Each encounter is identified by the location and run ID. 
| 07 | Each marriage is identified by SOLELY one marriage ID, a number given to each marriage in order (ie auto incremented) and is unique across runs. |
| 08 | Each type is identified by its unique name. |
| 09 | Each boss battle is identified by its run ID and boss number. |

<!-- |09| A marriage is considered when two pokemon from the same run are paired up. They must be Male & Female or Genderless & Genderless.| cionstraints rule -->

#### 2.2 Relationship rules

| ID | Relationship | Business Rule | 
|---|---|---|
| 10 | HAS_TYPE | Each species must have one or two types. Each type must belong to one or more species. |
| 11 | AVAILABLE_AT | Each location may have zero or more species available there. Each species may be available at zero or more locations. |
| 12 | RECORDS | Each run may have zero or more encounters. Each encounter must belong to exactly one run. |
| 13 | AT | Each location may have zero or more encounters. Each encounter must happen at exactly one location. |
| 14 | CAUGHT_AS | Each species may be met in zero or more encounters. Each encounter must be exactly one species when it is met. |
| 15 | CURRENTLY | Each species may be the current form of zero or more caught Pokemon. Each caught Pokemon must currently be exactly one species. |
| 16 | MARRIED_IN | Each marriage must include exactly two Pokemon. Each Pokemon may be in at most one marriage. |
| 17 | FIGHTS | Each run may have zero or more boss battles. Each boss battle must belong to exactly one run. |
| 18 | AGAINST | Each boss may be fought in zero or more boss battles. Each boss battle must be against exactly one boss. |
| 19 | IN_PARTY | Each boss battle must use one to three married couples (2, 4, or 6 Pokemon). Each Pokemon may be used in zero or more boss battles. |
| 20 | DIED_IN | Every battle may kill zero or more Pokemon. Each Pokemon may die in at most one battle, whether its a encounter or not. |
| 21 | WIPED_AT | Each location may be where zero or more runs were wiped. Each run may be wiped at no more than one location. |
| 22 | WIPED_BY | Each boss may wipe zero or more runs. Each run may be wiped by no more than one boss. |
| 23 | IN_LINE | Each species must belong to exactly one evolution line, named after its base form. Each base form may head one or more species in its line, including itself. |
| 24 | HELD_AT | Each location may host zero or more bosses. Each boss must be fought at exactly one location. A character taht is fought more than once, such as the rival or Evil Boss, is recorded as a separate boss for each fight. |


#### 2.3 Constraint rules

| ID | Business Rule | 
|---|---|
| 25 | **First-encounter rule:** A single run records only one encounter per location. |
| 26 | Every caught Pokemon must have a nickname of 1 to 10 characters. Pokemon that were not caught have no nickname. |
| 27 | Every caught Pokemon must have exactly one gender: Male, Female, or Genderless. |
| 28 | A Pokemon's current species starts as the species it was caught as. It can only change by evolving, and it must stay in the same evolution line. |
| 29 | A Pokemon that faints is dead and cannot be used in any battle after its death. Each death records exactly one cause: Boss Battle, Trainer, Wild, or Poison. |
| 30 | A marriage is formed when two caught Pokemon from the same run are paired up. Both must be alive, and they must be Male & Female or Genderless & Genderless. |
| 31 | If a Pokemon's partner dies, the surviving partner is widowed and cannot be used in battles after that battle. |
| 32 | Each marriage has exactly one status: Married or Widowed. When either partner dies, the marriage becomes Widowed. |
| 33 | Only Pokemon in a Married marriage may be used in a boss battle, and both partners must be in the same party. |
| 34 | Gym Leaders must be challenged in order of their badge number. This would be 1-8.|
| 35 | The Elite Four may be challenged only after all 8 badges are earned. They must be fought in their set order, followed by the Champion. |
| 36 | Each boss is fought at most once per run. |
| 37 | The Champion is the credits boss. A run is completed when the end credits roll after the Champion is defeated, and nothing after that is recorded for the run. |
| 38 | A Wiped run must record the location where it was wiped. If the wipe happened in a boss battle, the run must also record that boss. Runs that were not wiped record neither. |
| 39 | A location, species, or boss that has been used in a run cannot be deleted. |


#### 2.4 Derivation 

| ID | Business Rule | 
|---|---|
| 40 | A run's death count is the number of its Pokemon that have died. |
| 41 | A run's badges earned is the number of Gym Leader battles it won. |
| 42 | A couple is "Happy Ever After" if both partners are alive and still married when the end credits roll. |
| 43 | The win rate is the number of Won runs divided by the number of Won and Wiped runs. |


### 3 - 7. Entities, Relationships, Attributes and Domains, and Key constraints

#### 1. Location Entity
| Attribute | Domain |
|---|---|
| location_id (PK) | String data type. Uppercase letters, digits, and underscores only. Length constraint: 3 to 20 characters. Must be unique ('ROUTE_2', 'GRANITE_CAVE', 'PETALBURG_CITY'). |
| location_name | String data type. |
| location_type | ENUM data type. Acceptable values: 'Route', 'Town', 'City', 'Cave', 'Water', 'Building', 'Other'. |

#### 2. Type Entity
| Attribute | Domain |
|---|---|
| type_name (PK) | String data type. This is to prevent us from repeating the same type over and over again. |

#### 3. Species Entity
| Attribute | Domain |
|---|---|
| species_id (PK) | Integer data type. 1 to 386 (Pokédex number). |
| species_name | String data type. Length constraint: 1 to 10 characters. Must be unique, not case-sensitive. |
| type_1_name (FK) | String data type. References type_name in the Type entity (the primary type). Required. |
| type_2_name (FK) | String data type. References type_name in the Type entity (the secondary type). Can be null since not all Pokemon will have both types. Must be different from type_1_name. |
| evolution_line_id (FK) | Integer data type. References species_id in the Species entity. Will use the first pokedex number |
| evolution_stage | Integer data type. Range: 1 to 3. |
| is_legendary | Boolean data type. Default: FALSE. |

#### 4. Availability Entity (associative entity between Location and Species)
| Attribute | Domain |
|---|---|
| location_id (PK, FK) | String data type. References location_id in the Location entity. |
| species_id (PK, FK) | Integer data type. References species_id in the Species entity. |
| method (PK) | String data type. Acceptable values: 'Grass', 'Cave', 'Surfing', 'Old Rod', 'Good Rod', 'Super Rod', 'Rock Smash', 'Gift', 'Trade', 'Static'. |

#### 5. Boss Entity
| Attribute | Domain |
|---|---|
| boss_no (PK) | Integer data type. Range: 1 to 30. The boss's order in the story. Defined along with boss_role. |
| boss_name | String data type. Length constraint: 1 to 30 characters. |
| boss_role | String data type. Acceptable values: 'Rival', 'Gym Leader', 'Villain', 'Elite Four', 'Champion'. |
| badge_no | Integer data type. Null if the boss is not a Gym Leader. Range: 1 to 8. |
| location_id (FK) | String data type. References location_id in the Location entity (where the boss is fought). |
| ace_level | Integer data type. Range: 1 to 100. ||

#### 6. Run Entity
| Attribute | Domain |
|---|---|
| run_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each run. |
| start_time | DateTime data type. Defaults to the current date and time. Must be before end_time. |
| end_time | DateTime data type. Nullable while the run is in progress. |
| outcome | ENUM data type. Acceptable values: 'In Progress', 'Won', 'Wiped', 'Abandoned'. Default: 'In Progress'. | 
| wipe_location_id (FK) | String data type. References location_id in the Location entity. Required if the outcome is 'Wiped', NULL otherwise. |
| wipe_boss_no (FK) | Integer data type. References boss_no in the Boss entity. Can be null. |

#### 7. Encounter Entity (weak entity, owned by Run)
| Attribute | Domain |
|---|---|
| run_id (PK, FK) | Integer data type. References run_id in the Run entity. Together with location_id, forms the primary key. |
| location_id (PK, FK) | String data type. References location_id in the Location entity. |
| species_id (FK) | Integer data type. References species_id in the Species entity (the species it was met as). Must be available at this location. |
| current_species_id (FK) | Integer data type. References species_id in the Species entity (the species it is now). Must be in the same evolution line as species_id. NULL if not caught. |
| catch_result | ENUM data type. Acceptable values: 'Caught', 'Fled', 'Knocked Out'. |
| nickname | String data type. Length constraint: 1 to 10 characters. Required if caught, NULL otherwise. Unique within its run. |
| gender | ENUM data type. Acceptable values: 'Male', 'Female', 'Genderless'. Required if caught, NULL otherwise. |
| level_met | Integer data type. Range: 1 to 100.
| death_cause | ENUM data type. Acceptable values: 'Boss Battle', 'Trainer', 'Wild Pokemon', 'Poison'. Nullable (NULL means alive or not caught). |
| died_in_boss_no (FK) | Integer data type. Together with run_id, references the Boss Battle entity. Required only if death_cause is 'Boss Battle', NULL otherwise. |

#### 8. Marriage Entity
| Attribute | Domain |
|---|---|
| marriage_id (PK) | Integer data type with no negative values and a unique identifier for each marriage. |
| run_id (FK) | Integer data type. References run_id in the Run entity. Shared by both partners. |
| partner_1_location_id (FK) | String data type. Together with run_id, references the Encounter entity (the first partner). |
| partner_2_location_id (FK) | String data type. Together with run_id, references the Encounter entity (the second partner). Must be different from partner_1_location_id. |
| wedding_date | DateTime data type. Must fall between the run's start and end times. |
| status | ENUM data type. Acceptable values: 'Married', 'Widowed'. Default: 'Married'. |

#### 9. Boss Battle Entity (weak entity, owned by Run)
| Attribute | Domain |
|---|---|
| run_id (PK, FK) | Integer data type. References run_id in the Run entity. Together with boss_no, forms the primary key. |
| boss_no (PK, FK) | Integer data type. References boss_no in the Boss entity. |
| fought_at | DateTime data type. Must fall between the run's start and end times. |
| result | ENUM data type. Acceptable values: 'Won', 'Lost'. |

#### 10. Battle Party Entity (associative entity between Boss Battle and Encounter)
| Attribute | Domain |
|---|---|
| run_id (PK, FK) | Integer data type. Together with boss_no, references the Boss Battle entity. |
| boss_no (PK, FK) | Integer data type. Together with run_id, references the Boss Battle entity. |
| location_id (PK, FK) | String data type. Together with run_id, references the Encounter entity (the Pokemon used). |
| level_in_battle | Integer data type. Range: 1 to 100. Must not exceed the boss's ace_level. |

#### Relationships

| Relationship | Entities | Cardinality | Participation |
|---|---|---|---|
| PRIMARY_TYPE | TYPE – SPECIES | 1:N | Partial – Total |
| SECONDARY_TYPE | TYPE – SPECIES | 1:N | Partial – Partial |
| AVAILABLE_AT | LOCATION – SPECIES (through AVAILABILITY) | M:N | Partial – Partial |
| IN_LINE (recursive) | SPECIES – SPECIES | 1:N | Partial – Total |
| HELD_AT | LOCATION – BOSS | 1:N | Partial – Total |
| RECORDS (identifying) | RUN – ENCOUNTER | 1:N | Partial – Total |
| AT | LOCATION – ENCOUNTER | 1:N | Partial – Total |
| CAUGHT_AS | SPECIES – ENCOUNTER | 1:N | Partial – Total |
| CURRENTLY | SPECIES – ENCOUNTER | 1:N | Partial – Partial |
| MARRIED_IN | MARRIAGE – ENCOUNTER | 1:N | Total – Partial |
| FIGHTS (identifying) | RUN – BOSS_BATTLE | 1:N | Partial – Total |
| AGAINST | BOSS – BOSS_BATTLE | 1:N | Partial – Total |
| IN_PARTY | BOSS_BATTLE – ENCOUNTER (through BATTLE_PARTY) | M:N | Total – Partial |
| DIED_IN | BOSS_BATTLE – ENCOUNTER | 1:N | Partial – Partial |
| WIPED_AT | LOCATION – RUN | 1:N | Partial – Partial |
| WIPED_BY | BOSS – RUN | 1:N | Partial – Partial |

NOTE: TYPE takes part in two different relationships with SPECIES: PRIMARY_TYPE records the species' first type, and SECONDARY_TYPE records its second type, if it has one. SPECIES takes part in two different relationships with ENCOUNTER: CAUGHT_AS records the species the Pokemon was met as, and CURRENTLY records the species it is now after evolving.


### 8. Users and Access Scope

| User Type | Description | Access Scope |
|---|---|---|
| Runner | The person playing and logging Wedding Nuzlocke runs | No direct database access. The tracker app shows them their runs, encounters, marriages, boss battles, and stats. |
| Tracker app | The program used to log runs | Reads all game content; creates and updates runs, encounters, marriages, boss battles, and battle parties. Cannot delete data. |
| Content editor | The person who loads and fixes Pokémon Emerald's game data | Adds and edits locations, types, species, availability, and bosses. Cannot delete anything that has been used in a run. |
| Data analyst | Someone who studies difficulty and survival trends | Read-only access to all tables. |


### 9. Data Population

Game content (locations, types, species, availability, and bosses) comes from Pokemon Emerald's real game data. Run data (runs, encounters, marriages, boss battles, and battle parties) will be created for this project. Every table will have at least 15 rows, as required for the final implementation.

- Locations, Species, Pokemon Pokedex numbers, availability, and bosses will be taken from Bulbapedia, a very comprehensive fan made website for storing Pokemon data. (https://bulbapedia.bulbagarden.net/)
- Run data like nicknames or timestamps will be generated using the Faker library for Python (https://pypi.org/project/Faker/).

### Step II. Entity-Relationship Diagrams

(Chen notation diagram goes here.)
(Crow's Foot notation diagram goes here.) 
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

<!-- can die from poison and shit in gen3 -->
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


### 2. Business Rules
#### 2.1 Identity rules

| ID | Business Rule | 
|---|---|
| 01 | Each caught Pokemon can have only one nickname, and no two caught Pokemon can share a name in the same run.  |
| 02 | Each species is also identigied through a National Pokedex Number starting from #1 - #386. |
| 03 | Every location has one location ID and one name. There is no location that can share a name with another. | 
| 04 | Every boss encounter is identified by a boss number. 
| 05 | Each run is identified by exactly one run ID.
| 06 | Each encounter is identified by the location and run ID. 
| 07 | Each marriage is identified by SOLELY one marriage ID, a number given to each marriage in order.
| 08 | Each type is stored once with a unique name.
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


#### 2.3 Constraint rules

| ID | Business Rule | 
|---|---|
| 23 | **First-encounter rule:** A single run records only one encounter per location. |
| 26 | Every caught Pokemon must have a nickname of 1 to 10 characters. Pokemon that were not caught have no nickname. |
| 27 | Every caught Pokemon must have exactly one gender: Male, Female, or Genderless. |
| 28 | A Pokemon's current species starts as the species it was caught as. It can only change by evolving, and it must stay in the same evolution line. |
| 29 | A Pokemon that faints is dead and cannot be used in any battle after its death. Each death records exactly one cause: Boss Battle, Trainer, Wild, or Poison. |
| 30 | A marriage is formed when two caught Pokemon from the same run are paired up. Both must be alive, and they must be Male & Female or Genderless & Genderless. |
| 31 | If a Pokemon's partner dies, it is no longer usable after the battle where the pokemon died. |
| 32 | Each marriage has exactly one status: Married or Died. When either partner dies, the partner must die also. |
| 33 | Only Pokemon in a Married marriage may be used in a boss battle, and both partners must be in the same party. A widowed Pokemon cannot be used in boss battles. |
| 36 | Gym Leaders must be challenged in order of their badge number. This would be 1-8.|
| 37 | The Elite Four may be challenged only after all 8 badges are earned. They must be fought in their set order, followed by the Champion. |
| 38 | Each boss is fought at most once per run. |
| 39 | The Champion is the credits boss. A run is completed when the end credits roll after the Champion is defeated, and nothing after that is recorded for the run. |
| 41 | A Wiped run must record the location where it was wiped. If the wipe happened in a boss battle, the run must also record that boss. Runs that were not wiped record neither. |
| 42 | A location, species, or boss that has been used in a run cannot be deleted. |


#### 2.4 Derivation 

| ID | Business Rule | 
|---|---|
<!-- | 43 | A run's death count is the number of its Pokemon that have died.  |
| 44 | A run's badges earned is the number of Gym Leader battles it won.  |
| 46 | A couple is "Happy Ever After" if both partners are alive and still married when the end credits roll. |
| 47 | The win rate is the number of succesful Won runs divided by the number of completed runs. |


### 3 - 7. Entities, Relationships, Attributes and Domains, and Key constraints.

<!-- #### 1. Player Entity
| Attribute | Domain |
|---|---|
| player_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each player. |
| username | String data type with letters, digits, and underscores only. Length constraint: 6 to 20 characters. Must be unique. |
| email | String data type; follows the email format. Length constraint: 5 to 100 characters. Must be unique.  |
| password_hash| String data type; stores a hashed password; never plain text. Fixed length: 60 characters. |
| date_joined | DateTime data type. Defaults to the current date and time |
| account_status | ENUM data type. Acceptable values: 'Active', 'Suspended', 'Banned', 'Deleted'. Default: 'Active'.|
| total_xp | Integer data type. No negative values. Default 0. |

#### 2. Costume Entity
| Attribute | Domain |
|---|---|
| costume_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each costume. |
| costume_name | String data type. Length constraint: 1 to 40 characters. Must be unique.
| rarity | ENUM data type. Acceptable values: 'Common', 'Rare', 'Epic', 'Legendary'. Default: 'Common' |
| unlock_level| Integer data type. Range: 1 to 100. Default: 1. |
| is_halloween | Boolean data type. Default: FALSE. |
| is_archived | Boolean data type. Default: FALSE. |

#### 3. Map Entity
| Attribute | Domain |
|---|---|
| map_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each map.|
| map_name | String data type. Length constraint: 1 to 40 characters. Must be unique, not case-sensitive.
| borough | ENUM data type. Acceptable values: 'Manhattan', 'Brooklyn', 'Queens', 'Bronx', 'Staten Island' |
| real_location| String data type. Length constraint: 1 to 100 characters. Nullable (NULL if the map is fictional) |
| latitude | Decimal data type. Range: 40.47 to 40.92 |
| longitude | Decimal data type. Range: -74.26 to -73.70 |
| map_size | ENUM data type. Acceptable values: 'Small', 'Medium', 'Large'
| max_players | Integer data type. Range: 1 to 4. Default: 4.
| is_halloween_map | Boolean data type. Default: FALSE
| is_archived | Boolean data type. Default: FALSE.

#### 4. Room Entity (weak entity, owned by MAP)
| Attribute | Domain |
|---|---|
| map_id (PK, FK) | Integer data type. References map_id in the Map entity. Together with room_no, forms the primary key.|
| room_no (PK) |  Integer data type. Range: 1 to 40. Unique only within its map.
| room_name | String data type. Length constraint: 1 to 40 characters. Must be unique within its map.
| floor_level | Integer data type. Range: -5 to 15 (negative values mean underground) Default 1. |
| is_staging_area | Boolean data type. There is exactly one staging area per map, outside the investigating location. Default: FALSE.  

#### 5. Ghost Type Entity
| Attribute | Domain |
|---|---|
| ghost_type_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each ghost type|
| ghost_type_name | String data type. Length constraint: 1 to 20 characters. Must be unique, not case sensitive.
| description | String data type. Length constraint: 1 to 500 characters |
| hunt_sanity_threshold | Integer data type. Range: 0 to 100. Default: 50.
| hunt_speed | Decimal data type. Range: 0.5 to 5.0 (meters per second).
| is_archived | Boolean data type. Default: FALSE.

#### 6. Evidence Type Entity
| Attribute | Domain |
|---|---|
| evidence_type_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each evidence type|
| evidence_name | String data type. Length constraint: 1 to 30 characters. Must be unique, not case-sensitive.
| detected_with | String data type. Length constraint: 1 to 30 characters.
| description | String data type. Length constraint: 1 to 500 characters. Nullable.

#### 7. Investigation Entity
| Attribute | Domain |
|---|---|
| investigation_id (PK) | Integer data type, auto-incremented. No negative values. Unique identifier for each investigation. |
| map_id (FK) | Integer data type. References map_id in the Map Entity. 
| ghost_type_id (FK) | Integer data type. References ghost_type_id in the Ghost Type entity (the actual ghost).|
| guess_ghost_type_id (FK) | Integer data type. References ghost_type_id in the Ghost Type entity (the team's guess). Nullable. |
| ghost_room_no (FK) | Integer data type. Together with map_id, references the Room entity (the ghost's favorite room). |
| difficulty | ENUM data type. Acceptable values: 'Rookie', 'Detective', 'Veteran', 'Nightmare'. Default: 'Rookie'. |
| start_time | DateTime data type. Must be before end_time. |
| end_time | DateTime data type. Nullable while the investigation is in progress.
| final_hour | Integer data type. Range: 0 to 6 (0 = midnight, 6 = dawn). Default: 0
| outcome | ENUM data type. Acceptable values: 'In Progress', 'Victory', 'Misidentified', 'Team Wiped', 'Abandoned'. Default: 'In Progress'.

#### 8. Investigation Investigator Entity (associative entity between Player and Investigation)
| Attribute | Domain |
|---|---|
| investigation_id (PK, FK) | Integer data type. References investigation_id in the Investigation entity. |
| player_id (PK, FK) | Integer data type. References player_id in the Player Entity. 
| costume_id (FK) | Integer data type. References costume_id in the Costume entity. Nullable (NULL means the default outfit). |
| killed_in_hunt_no (FK) | Integer data type. Together with investigation_id, references the Hunt entity. Nullable (NULL means the investigator was not killed). |
| joined_at | DateTime data type.|
| left_at | DateTime data type. Nullable (NULL means the investigator did not quit). Must fall between the investigation's start and end times. |
| is_host | Boolean data type. Default: FALSE. Exactly one host per investigation.|
| final_sanity | Integer data type. Range 0 to 100. Nullable until the investigation ends.|
| xp_earned | Integer data type. Range: 0 to 22,500. Recorded when the investigation ends. |

#### 9. Hunt Entity (weak entity, owned by Investigation)
| Attribute | Domain |
|---|---|
| investigation_id (PK, FK) | Integer data type. References investigation_id in the Investigation entity. |
| hunt_no (PK) | Integer data type. Range: 1 to 50. Unique only within its investigation |
| night_hour | Integer data type. Range: 0 to 5.|
| started_at | DateTime data type. Must fall between the investigation's start and end times.|
| duration_sec | Integer data type. Range: 10 to 60 seconds.
| team_avg_sanity | Integer data type. Range 0 to 100.


#### Relationships

| Relationship | Entities | Cardinality | Participation | 
|---|---|---|---|
| CONTAINS (identifying) | MAP – ROOM | 1:N | Total – Total | 
| HAS_EVIDENCE | GHOST_TYPE – EVIDENCE_TYPE | M:N | Total – Total | 
| TAKES_PLACE_ON | MAP – INVESTIGATION | 1:N | Partial – Total | 
| HAUNTED_BY | GHOST_TYPE – INVESTIGATION | 1:N | Partial – Total |
| GUESSED_AS | GHOST_TYPE – INVESTIGATION | 1:N | Partial – Partial | 
| FAVORITE_ROOM | ROOM – INVESTIGATION | 1:N | Partial – Total | 
| JOINS | PLAYER – INVESTIGATION (through INVESTIGATION_INVESTIGATOR) | M:N | Partial – Total | 
| WEARS | COSTUME – INVESTIGATION_INVESTIGATOR | 1:N | Partial – Partial | 
| TRIGGERS (identifying) | INVESTIGATION – HUNT | 1:N | Partial – Total | 
| KILLED_IN | HUNT – INVESTIGATION_INVESTIGATOR | 1:N | Partial – Partial | 

NOTE: GHOST_TYPE takes part in two different relationships with INVESTIGATION: HAUNTED_BY records the actual ghost, and GUESSED_AS records the team's guess. -->

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
| type_name | String data type. This is to prevent us from repeating the same type over and over again. |

#### 3. Species Entity
| Attribute | Domain |
|---|---|
| species_id (PK) | Integer data type. 1 to 386 (Pokédex number). |
| species_name | String data type. Length constraint: 1 to 10 characters. Must be unique, not case-sensitive. |
| type_1_id (FK) | Integer data type. References type_id in the Type entity (the primary type). Required. |
| type_2_id (FK) | Integer data type. References type_id in the Type entity (the secondary type). Can be null. Must be different from type_1_id. |
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
| badge_no | Integer data type. Range: 1 to 8. |
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
| level_met | Integer data type. Range: 1 to 100. Must be within the location's level range for the species. |
| death_cause | ENUM data type. Acceptable values: 'Boss Battle', 'Trainer', 'Wild Pokemon', 'Poison'. Nullable (NULL means alive or not caught). |
| died_in_boss_no (FK) | Integer data type. Together with run_id, references the Boss Battle entity. Required only if death_cause is 'Boss Battle', NULL otherwise. |

#### 8. Marriage Entity
| Attribute | Domain |
|---|---|
| marriage_id (PK) | Integer data type. No negative values. Unique identifier for each marriage. |
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
| MARRIED_IN | MARRIAGE – ENCOUNTER | 1:2 | Total – Partial |
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

Since the game is fictional, most data will be created for this project. Every table will have at least 15 rows, as required for the final implementation. 

- Game Locations and Encounters here will be populated using Faker Library from Python (https://pypi.org/project/Faker/).
- Pokemon and its associated Species ID will be from Bulbapedia, a fan made website to storing Pokemon data. (https://bulbapedia.bulbagarden.net/)
- 
### Step II. Entity-Relationship Diagrams

(Chen notation diagram goes here.)
(Crow's Foot notation diagram goes here.) 
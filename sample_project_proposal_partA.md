# Gotta Ghost 'Em All
*Co-op survival horror game, with a Halloween event*

Name: Yoomin Song

### Step I. Database Requirements and Planning

#### 1. Purpose and Goals
**Gotta Ghost 'Em All** is a fictional co-op survival horror game about ghost hunting, inspired by Phasmophobia. One to four players spend one night in a haunted NYC location, such as a closed subway station or an abandoned hospital. Each night has one ghost. The team wins if at least one investigator survives until dawn and the team identifies the ghost type correctly; otherwise, the team loses. If no investigator is left before dawn and at least one of them quits, the investigation is abandoned. When the ghost starts a hunt, any investigator it catches dies. Each ghost type a player identifies correctly is added to their Ghost Journal, and the end-goal is to catch 'em all. A Halloween map and Halloween costumes are available every October. 

The purpose of this database is to store the game's content and the result of every night played to ensure content can be safely changed and answer important questions such as "Which ghost type wipes teams most often?" to maintain game-balance and "Does a Halloween event actually increase the number of users?"

#### 1.1 Glossary 
*(Note to students: Include a glossary only if your project uses terms a reader may not know.)*

| Term | Meaning |
|---|---|
| Investigation | One night on one map against one ghost, played by one to four players. |
| Investigator | A player taking part in a specific investigation. |
| Hunt | A period when the ghost actively attacks; an investigator it catches dies. |
| Victory | At least one investigator is alive at dawn, and the team's guess is correct.|
| Quit | An investigator leaves before the night ends. They can no longer be killed and earn no XP. |
| Staging area | The team's van outside the location. It is a safe area where no ghost activity happens. |
| Ghost Journal | The set of ghost types a player has identified correctly. |
| Halloween event | October 1, 00:00 to October 31, 23:59, New York time. |
| Archived | Content no longer offered in new investigations but kept for history. |

### 2. Business Rules
#### 2.1 Identity rules

| ID | Business Rule | 
|---|---|
| 01 | Each player has exactly one player ID, one username, and one email address. No two players share any of these. Usernames and email addresses are not case-sensitive. |
| 02 | Each player has exactly one password, which is stored only in hashed form and never as plain text. |
| 03 | Each player has exactly one account status at a time: Active, Suspended, Banned, or Deleted. | 
| 04 | Each map, costume, ghost type, and evidence type has exactly one name, and no two of the same kind share a name. 
| 05 | Each map is located in exactly one of NYC's five boroughs and has exactly one set of coordinates. 
| 06 | Each investigation is identified by exactly one investigation ID. 
| 07 | Each room is identified by its map plus a room number. Room names are unique within a map. 
| 08 | Each hunt is identified by its investigation plus a hunt number (1, 2, 3, ...). 

#### 2.2 Relationship rules

| ID | Relationship | Business Rule | 
|---|---|---|
| 09 | CONTAINS | Each map must contain 2 to 40 rooms. Each room must belong to exactly one map. | 
| 10 | HAS_EVIDENCE | Each ghost type must leave exactly three evidence types. Each evidence type must be left by one or more ghost types. |
| 11 | TAKES_PLACE_ON | Each map may host zero or more investigations. Each investigation must take place on exactly one map. | 
| 12 | HAUNTED_BY | Each ghost type may haunt zero or more investigations. Each investigation must be haunted by exactly one ghost type. | 
| 13 | GUESSED_AS | Each ghost type may be guessed in zero or more investigations. Each investigation may have at most one guessed ghost type. 
| 14 | FAVORITE_ROOM | Each room may be the ghost's favorite room in zero or more investigations. Each investigation must have exactly one favorite room, where a hunt is most likely to start. |
| 15 | JOINS | Each player may join zero or more investigations. Each investigation must include one to four players, exactly one of whom is the host. | 
| 16 | WEARS | Each costume may be worn by zero or more investigators. Each investigator may wear at most one costume. | 
| 17 | TRIGGERS | Each investigation may have zero or more hunts. Each hunt must occur in exactly one investigation. |
| 18 | KILLED_IN | Each hunt may kill zero or more investigators. Each investigator may be killed in at most one hunt. | 

#### 2.3 Constraint rules

| ID | Business Rule | 
|---|---|
| 19 | Only Active players may join an investigation, and a player may be in only one investigation at a time. | 
| 20 | Each map allows one to four players, and an investigation may not have more investigators than its map allows. | 
| 21 | The Halloween map and Halloween costumes are available only during the Halloween event (October 1, 00:00 to October 31, 23:59, New York time) by default.|
| 22 | The game has at least 15 evidence types. Each ghost type has a unique set of three evidence types and can be identified from its evidence (i.e., no two ghost types may leave the same set of three evidence types). |
| 23 | Map coordinates must fall within NYC's boundaries. Each map has exactly one staging area. | 
| 24 | The ghost's favorite room must be on the investigation's map and cannot be the staging area. The ghost type and favorite room cannot change once the investigation starts. 
| 25 | Each investigator starts the night with 100 sanity, which can fall to 0 but never rise above 100. The ghost may start a hunt only when the team's average sanity is at or below that ghost type's hunt threshold.|
| 26 | A hunt can kill only investigators of its own investigation who are alive and have not quit. Investigators in the staging area cannot be killed. |
| 27 | The team may submit only one guess per investigation, and it cannot be changed once submitted. | 
| 28 | An investigator may quit at any time before dawn. An investigator cannot be both killed and quit. | 
| 29 | A player may wear a costume only if their level is at least the costume's unlock level. | 
| 30 | A map, room, ghost type, or costume that has been used in an investigation cannot be deleted, only archived.
| 31 | The night runs from hour 0 (midnight) to hour 6 (dawn). Each investigation ends with exactly one outcome: Victory (win): hour 6 is reached with at least one investigator alive, and the guess is correct; Misidentified (loss): hour 6 is reached with at least one investigator alive, but the guess is wrong or missing; Team Wiped (loss): every investigator was killed; Abandoned (neither win nor loss): no investigator is left before dawn and at least one of them quit.

#### 2.4 Derivation 

| ID | Business Rule | 
|---|---|
| 32 | A player's total XP is the sum of the XP they earned in all investigations. Player level = 1 + (total XP / 5,000, rounded down), up to 100. | 
| 33 | XP earned = base XP for the outcome (Victory: 1,500; Misidentified: 1,000; Team Wiped: 300; Abandoned: 0) × the difficulty multiplier (Rookie 1.0, Detective 1.5, Veteran 2.0, Nightmare 3.0). × 5.0 if the investigation started during the Halloween event. A player who quits before an investigation ends earns 0 XP.  XP is halved for an investigator who was killed. The amount is recorded when the investigation ends, and changes to these values in the future do not change past results.|
| 34 | A player's Ghost Journal contains each ghost type correctly identified in an investigation they took part in. The journal is complete when it contains every ghost type in the game. |
| 35 | Each investigator ends the night with exactly one status: Survived (alive at dawn), Killed (caught in a hunt), or Quit (left before dawn). The status is derived from killed_in_hunt_no and left_at and is not stored.|

### 3 - 7. Entities, Relationships, Attributes and Domains, and Key constraints.

#### 1. Player Entity
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

NOTE: GHOST_TYPE takes part in two different relationships with INVESTIGATION: HAUNTED_BY records the actual ghost, and GUESSED_AS records the team's guess.


### 8. Users and Access Scope

| User Type | Description | Access Scope |
|---|---|---|
| Runner | The person playing and logging Wedding Nuzlocke runs | No direct database access. The tracker app shows them their runs, encounters, marriages, boss battles, and stats. |
| Tracker app | The program used to log runs | Reads all game content and creates and updates runs, encounters, marriages, boss battles, and battle parties. Cannot delete data. |
| Content editor | The person who loads and fixes Pokémon Emerald's game data | Adds and edits locations, types, species, availability, and bosses. Cannot delete anything that has been used in a run. |
| Data analyst | Someone who studies difficulty and survival trends | Read-only access to all tables. |
| Database administrator | The person who maintains the database | Full access to structure changes, backups, and user accounts. |

### 9. Data Population

Since the game is fictional, most data will be created for this project. Every table will have at least 15 rows, as required for the final implementation. 


- Real map locations come from NYC Open Data, Individual Landmark Sites (https://opendata.cityofnewyork.us).
- Player and investigation data are generated with the Python Faker library or Mockaroo (https://www.mockaroo.com).

### Step II. Entity-Relationship Diagrams

(Chen notation diagram goes here.)
(Crow's Foot notation diagram goes here.) 
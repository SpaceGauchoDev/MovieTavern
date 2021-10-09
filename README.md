# Streaming System Wishlist:
*Some day, I might make a comprehensive movie streaming platform, using OBS, VLC and some independent streaming service, it might even include a dedicated chat service, these are things i wish it had*

## Systems: 
	**- System remote commands:**
	*System should have a way to perform basic operations commands from a open API*
		-	API should enforce some safety mechanisms (DDOS and injection protection for all commands)
		-	Skip movie being played [Admin]
		- 	Restart system	[SuperAdmin]
		- 	Restart VLC	[Admin]
		- 	Restart OBS	[Admin]
		-	Get info on movie being currently played [User]

	**- System mounting script:**
	*Whole system should be mountable on a server by running a single script, bat or iso file*

	**- System save state:**
	*Updates a save file of the system status every X time in case the system goes down unexpectedly*
	
## Entities:
*All physical file type entities should be placed alongside a metadata file that persists all references to its attributes and info, when an entity links to another, it should use its UID for virtual path lookup*

### Base Types		
	- [UID] extends
		- id: string
		
	- [Entity] extends [UID]
		- filePath: string
		- fileName: string
		- isPhysicalFile: bool
		- entityName: string
		- toMeta() (abstract) -> saves all attributes to plain text meta file, when a attribute is another [Entity], save its UID, interacts with [FileSystem]
		- fromMeta() (abstract) -> reads all attributes from plain text meta file and returns a runtime [Entity], when a attribute is a UID, return its fromMeta() method, interacts with [FileSystem]
		
### Users
	- [APIConsumer] extends [Entity]
		- APIKey: string
		
	- [User] extends [Entity]
		- userName: string
		- userEmail: string
		- hashedEmailPassCombo: string
	
	- [SuperAdmin] extends [User]

	- [Admin] extends [User]
	
### Runtime:
	- [ExecuteCommand]
		*executes commands to the various other systems*
		*recieves commands from API*
		
	- [FileSystem]
		- getEntity([UID]):[Entity]
		- setEntity([Entity]): boolean
		
	- [LoggingSystem]
		*logs events into a plaintext file* 
		
	- [SystemState]
		- getState():[State]
		
	- [UsersSystem]
		- signIn():bool
		- signOut():bool
		- signedInAdmins: list ([Admin])
		- signedInSuperAdmins: list ([SuperAdmin])
		- signedInAPIConsumer: list ([APIConsumer])
	
### Configuration:
	- [SystemConfig] extends [Entity]
		- audioLanguage: [Language]
		- subtitleLanguage: [Language]
		- audioLanguage: [Language]
		- playlists: list([Playlist])
		- superAdmins: list([SuperAdmin])
		- admins: list([Admin])
		
	- [State] extends [Entity]
		- upTime: time
		- ???		
	
### Domain:		
	- [Movie] extends [Entity]
		- tags: list([Tag])
		- playPoints: list([PlaylistPlaypoint])
		- subtitles: list([Subtitle])
		- spokenAudioTracks: list([Language])
		- info: [MovieInfo]
		
	- [MovieInfo] extends [Entity]
		- mayoritaryOriginalLanguage: [Language]
		- originalTitle: string
		- englishTitle: string
		- runtime: time
		- imdbLink: string
	
	- [Subtitle] extends [Entity]
		- language [Language]
		- isCC: bool
		
	- [Playlist] extends [Entity]
		- list([Movie])
		
	- [Tag] extends [Entity]
		- displayName: string


### Resettable attributes, might not need:
	- [Resettable] (abstract)
		- hasChangedFromDefault() (abstract)
		- resetToDefault() (abstract)
		
	- [R_Text] extends [Resettable]
		- currentText: string
		- defaultText: string
		
	- [R_Decimal] extends [Resettable]
		- currentDecimal: float
		- defaultDecimal: float

	- [R_Integer] extends [Resettable]
		- currentInteger: int
		- defaultInteger: int

	- [R_Time] extends [Resettable]
		- currentTime: time
		- defaultTime: time

	- [R_Boolean] extends [Resettable]
		- currentBoolean: bool
		- defaultBoolean: bool

## Features: 
	**- OBS auto pause:**
	*If OBS detects a disconnection with streaming service, it should auto pause VLC, and resume it once conectivity is restored*

	**- VLC "randomnes":**
	*VLC playlists should enforce a better feeling randomness*
		-	All movies in a list have some sort of metadata called playedPoint
		-	When a playlist starts playing, all movies should have zero playedPoint
		-	If a movie has been played it should gain a playedPoint
		-	New random movies to play are chosen among the list that has less playedPoint
		- 	Played points should persist in case the system goes down and has to be restarted
		-	Played points should be capable of resetting to zero with a command
		-	A new movie added to a running playlist should have zero playedPoint

## UI: 
* UI interfaces available on system server*
	**- Playlist Editor:**
	*User should be able to create bespoke VLC compatible playlists from a dialogue*
		-	Users should be able to choose movies individually
		-	Users should be able to choose from a set of tags to filter movies
		-	Users cannot import playlists created outside of this flow
		
	**- Tag Editor:**
	*Users should be able to create tags from a dialogue*
		- Tags should be defined in a plaintext in a XML style master movie
		- Users should be able to assign tags to movies and to playlists
		- Users cannot create or assign tags outside of this flow
		
	**- Movie Import:**	
	*Movies should be able to be imported into a local repository in a painless way*
		-	All movies should have a UID for lookups
		-	All movies should be placed into a movie structure from a user friendly dialogue
		-	When a movie is imported subtitles should be imported as well
		-	All movies should contain some plain text metadata for the system to pull info from
		-	Movie importing should offer to add genre tags 
		-	Movie importing should offer to create genre tags
		-	Users should not be able to import movies outside of this flow 
		- 	Users should be able to bulk import movies if they adhere to certain file structure

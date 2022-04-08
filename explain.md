## ICE PROTOCOL EXPLAINATION

This contract simply helps store files, documents multimedia e.t.c and also helps to retrieve these documents as at when needed.

### Ice.sol Contract

This specific contract is the library that houses the logic for retrieving data.

```solidity

library IceSort {
     using SafeMath for uint;

```

It consist of a struct which houses an ID(storage pointer) to house the location of a particular file in the mapping, it also have a Index of prev and next of each order, there is also a bool(this indicates the state of a node, it can either be active or inactive)

It consists of a getOrder function => This function simply takes in a mapping as a parameter and a uint which points to the position of an order in the mapping and returns the corresponding struct of whatever is stored in that position.

It consists of a addToSortOrder function => This function adds to the mapping that houses the sortOrder struct by increasing its index.

It consists of a stichSortOrder function => This function simply help to preserve the order in case of a deletion  

It consists of a getIndexes function => this function returns the indexes of files or document and it also has a cap limit of numbers it can return which is 20, i.e the indexes represent the location in the mapping, it returns what is in the location in this case the struct and it also possess the capability of either returning files in ascending order or descending order of sorting

It consists of a condValidSortOrder function => it checks to see if the group order is valid.

### IdentityRegisterInterface Contract

This is an interface that manages identity (ETHEREUM IDENTITY NUMBER) ERC 1484

### IceGlobal.sol Contract

This specific contract is the library that houses the logic file management
```solidity
library IceGlobal {
    using SafeMath for uint;
    using SafeMath8 for uint8;
```

### IceFMSAdv.sol Contract
#### This is a Library

This contract imports the IceGlobal.sol and IceSort.sol
```solidity
library IceFMSAdv {
    using SafeMath for uint;
    
    using IceGlobal for IceGlobal.GlobalRecord;
    using IceGlobal for IceGlobal.Association;
    using IceGlobal for IceGlobal.UserMeta;
    using IceGlobal for mapping (uint => bool);
    using IceGlobal for mapping (uint8 => IceGlobal.ItemOwner);
    using IceGlobal for uint;
    
    using IceSort for mapping (uint => IceSort.SortOrder);
```
it uses the GlobalRecord for getting the global record of a given item
it uses the GlobalAssociation for getting the globalfile asociation with EIN(ethereum identity number)
it uses a mapping in the iceglobal to track the number of times an item as been shared
it uses a mapping in the iceglobal for tracking the EIN which a particular file have been sent to
it uses the UserMeta from IceGlobal for detecting the state of a file if it exists or not
it uses a mapping in the IceSort which tracks a items(structs) in a mapping

#### Events

Event SharingCompleted => this event emits the EIN of the sender and the recipient after sharing have been completed.

Event SharingRejected => this event emits the EIN of the sender and the recipient after sharing have been rejected.

Event SharingRemoved => this event emits the EIN of the sender and the recipient after sharing have been removed.

#### Functions


##### Stamping Functions

function shareItemToEINs => This function is used to share an item to other users, this function can only be called by the owner of the item. This is an external function.
- It takes in a 2D mapping of uint to another uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes in a 2D mapping of uint to another uint that uses a Association as value(this gets the global file association with EIN for detecting the item/file data) and assigns it to a storage pointer of _globalItems
- It takes in a 2D mapping of uint to another uint that uses a SortOrder as value(this gets the struct a value, this struct contains info of a file like the ID, Next ID, PrevID) and assigns it to a storage pointer of _totalSharedOrdermapping
- It takes in a mapping of uint to another uint this is used to track share count of a particular item and assigns it a storage pointer of _shareCountMapping
- It takes in a 2D mapping of uint to another uint that uses a bool as value(this mapping tracks the entire users that habve been blacklisted) and assigns it to a storage pointer of _blacklist
- It takes in GlobalRecord this is a struct which houses the record for a particular item and assigns it a storage pointer of _rec
-It takes a uint which indicates the unique number of the user who initiates the sharing of the item
-It takes in an array of _toEINs, this simply collates the total EINs to which the item would be shared.

The above function contains an if block to check if a EINs is being blaclisted or not, it does a forLoop of the total length EINs passed into it, if a particular address is blacklisted, it doesnt share to it and its count of total people the item is shared with is adjusted accordingly, and emits SharingRejected.

If the EINs is not blacklisted it simply sends to it and updates the count accordingly and triggers the emit SharingCompleted.



function _shareItemToEIN => This function is used to share an item to just a single user, this function can only be called by the owner. This is an internal function.
- It takes in a mapping of uint to a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes in a 2D mapping of uint to another uint that uses a Association as value(this gets the global file association with EIN for detecting the item/file data) and assigns it to a storage pointer of _globalItems
- It takes in a mapping of uint to a struct as value(this gets the mapping of all sahred order) and assigns it to a storage pointer _shareOrder
- It takes in a mapping of uint to a uint as value(this gets the mapping of all shared cound of an item) and assigns it to a storage pointer of _sharedCount
- It takes in GlobalRecord this is a struct which houses the record for a particular item and assigns it a storage pointer of _rec
-It takes in a uint of _toEIN, this simply accommodate the specific EINs the item is to be shared with.

function removeAllShares => This function is used to remove shares of an item
- It takes in a 2D mapping of uint to another uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes a uint which represents the ein of the user
- It takes in the struct that contains addition information of a file and assigns it _globalItemIndividual
- It contains a 2D mapping of uint to uint which takes in a struct as a value  and assigns it _shareOrdermapping
- It contains a mapping of uint to another unit that counts the share count of an item and assigns it _shareCountmapping

It contains an if block that checks the amount of eins that a particular file have been checked through and stores it into an array and then calls the removeShareFromEINs which removes the shared item from multiple uses array stored in memory.

function removeShareFromEINs => This function is used to remove an item from all EINs that have the particular item
- It takes in a 2D mapping of uint to another uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes a uint which represents the ein of the user
- It takes in the struct that contains addition information of a file and assigns it _globalItemIndividual
- It contains a 2D mapping of uint to uint which takes in a struct as a value  and assigns it _shareOrdermapping
- It contains a mapping of uint to another unit that counts the share count of an item and assigns it _shareCountmapping

It contains a while loop that removes all items passed into the _fromEINs by calling the _removeShareFromEIN function 

function _removeShareFromEIN =>  This function is used to remove an item from a particular EIN
- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes a uint which represents the ein of the user
- It takes in the struct that contains addition information of a file and assigns it _globalItemIndividual
- It contains a mapping of uint which takes in a struct as a value  and assigns it _shareOrdermapping
- It contains a mapping of uint to another unit that counts the share count of an item and assigns it _shareCountmapping

This function simply defines a variable called curIndex which is assigned the mapping of a particular EIN, it now checks for if the mapping exist for the particular EIN , if the mapping exist with the EIN included, it removes the the share from global item mappings by calling the removeFromGlobalItemsMapping from IceGlobal, it then stiches the order
it then emits a SharingRejected event.

function removeSharingItemBySharee =>  This function is used to remove an item from a particular EIN by using the item receiver as a means to locate.
- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It takes a uint which represents the ein of the recipient user
- It takes in the struct that contains addition information of a file and assigns it _globalItemIndividual
- It contains a mapping of uint which takes in a struct as a value  and assigns it _shareOrdermapping
- It contains a mapping of uint to another unit that counts the share count of an item and assigns it _shareCountmapping

This calls the _renmoveShareFromEIN function and removes the item.

##### Stamping Functions

function initiateStampingOfItem => This function is sued to initiate stamping of an item by the owner

- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It contains a mapping of uint which takes in a struct as a value  and assigns it _stampingReqOrdermapping 
- It contains a mapping of uint to another uint and assigns it _stampingReqCountmapping which tracks the mapping of all stamping request
- It takes a uint which is the owner of the EIN of the user who initiated the stamping of the item and assigns it _ownerEIN
- It takes a uint which is the recipient who is supposed to stamp the item and assigns it _
_reciepientEIN
- It takes a uint which is the index of the item and assigns it _itemIndex
- It takes a uint which is the count of the number of items the owner owns
- It takes the association struct of the item in question
- It takes the GlobalRecord struct of the item in question and assigns it _record
- It takes a 2D mapping of uint to mapping of uint and a bool as a value this tracks the blacklisted users.
- it takes takes in a registry which serves as a pointer to the ERC-1484 identity registry.

This function checks if the item to be stamped is valid, and then checks if it is unstamped, also it checks to see it the EIN exists, and checks to see if both the owner EIN and Reciepient EIN are unique and finally checks if the owner can send file to the recipient (if the owner of the file havent be blacklisted)
It then initiates the stamping, and then references the item for the recipient and increases the index and finally indicates who should stamp and where to stamp

function acceptStamping => This function allows the proposed recipient to accept the stamping of an item

- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It contains a mapping of uint which takes in a struct as a value  and assigns it _stampingOrdermapping
- It contains a mapping of uint to another uint and assigns it _stampingCountmapping which tracks the mapping of all completed stamping request
- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of _stampingsReq
- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of _stampingReqOrdermapping
- it takes a mapping of an uint to another uint which tracks all counts of stamping requests
- It creates the association struct 
- It takes the uint of the EIN of the person / recipient to stamp
- It takes the uintof the index of the item present in the stamping requests mapping of the recipient

This function checks if the item is a valid item, then adds the item to the list of items of the recipient to be stamped, then removes the stamping request from request order and stich it for the recipient and finally updates the association struct item (stampingCompleted)

function cancelStamping => This function is used to cancel stamping either by the owner or the recipient

- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It contains a mapping of uint which takes in a struct as a value  and assigns it _stampingReqOrdermapping 
- It contains a mapping of uint to another uint and assigns it _stampingReqCountmapping which tracks the mapping of all stamping request
- It takes the uint of the EIN of the person / recipient to stamp
- It takes the uintof the index of the item present in the stamping requests mapping of the recipient
- It creates the association struct

This function checks if the item is valid and checks if it has an initiated stamping and also if it hasnt completed stamping, it then proceeds to remove the item from the stamping request of the recipient by calling the _removeStampingReq function and stiches the order and then resets the association struct and finally delete the shares of the particular owner EIN.

function _removeStampingReq => This function removes the stamping of an item and it is a private function

- It takes in a mapping of uint that uses a GlobalRecord as value(this gets the global record of an item) and assigns it to a storage pointer of self
- It contains a mapping of uint which takes in a struct as a value  and assigns it _stampingReqOrdermapping 
- It contains a mapping of uint to another uint and assigns it _stampingReqCountmapping which tracks the mapping of all stamping request
- It takes the uint of the EIN of the person / recipient to stamp
- It takes the uintof the index of the item present in the stamping requests mapping of the recipient
- It creates the association struct


### IceFMS.sol Contract

This contract is a library imports  the following 

``` solidity
import "./SafeMath.sol";
import "./SafeMath8.sol";

import "./IceGlobal.sol";
import "./IceSort.sol";

import "./IceFMSAdv.sol";

```

``` solidity

library IceFMS {
    using SafeMath for uint;
    using SafeMath8 for uint8;
    
    using IceGlobal for IceGlobal.GlobalRecord;
    using IceGlobal for IceGlobal.Association;
    using IceGlobal for IceGlobal.UserMeta;
    using IceGlobal for mapping (uint => bool);
    using IceGlobal for mapping (uint8 => IceGlobal.ItemOwner);
    using IceGlobal for mapping (uint => mapping (uint => IceGlobal.Association));
    using IceGlobal for uint;
    
    using IceSort for mapping (uint => IceSort.SortOrder);
    using IceSort for IceSort.SortOrder;
    
    using IceFMSAdv for mapping (uint => mapping(uint => IceGlobal.GlobalRecord));
```

###### STRUCTURES
 ``` solidity
      struct FileMeta {
        bytes32 name; // to store the name of the file
        
        bytes32 hash; // to store the hash of file
        bytes22 hashExtraInfo; // to store any extra info if required
        
        bool encrypted; // whether the file is encrypted
        bool markedForTransfer; // Mark the file as transferred
        
        uint8 protocol; // store protocol of the file stored | 0 is URL, 1 is IPFS
        uint8 transferCount; // To maintain the transfer count for mapping
        
        uint8 hashFunction; // Store the hash of the file for verification | 0x000 for deleted files
        uint8 hashSize; // Store the length of the digest
        
        uint32 timestamp;  // to store the timestamp of the block when file is created
    }

        struct File {
        // File Meta Data
        IceGlobal.GlobalRecord rec; // store the association in global record

        // File Properties
        bytes protocolMeta; // store metadata of the protocol
        FileMeta fileMeta; // store metadata associated with file

        // File Properties - Encryption Properties
        mapping (address => bytes32) encryptedHash; // Maps Individual address to the stored hash

        // File Other Properties
        uint associatedGroupIndex; // to store the group index of the group that holds the file
        uint associatedGroupFileIndex; // to store the mapping of file in the specific group order
        uint transferEIN; // To record EIN of the user to whom trasnfer is inititated
        uint transferIndex; // To record the transfer specific index of the transferee

        // File Transfer Properties
        mapping (uint => uint) transferHistory; // To maintain histroy of transfer of all EIN
    }

     struct Group {
        IceGlobal.GlobalRecord rec; // store the association in global record

        string name; // the name of the Group

        mapping (uint => IceSort.SortOrder) groupFilesOrder; // the order of files in the current group
        uint groupFilesCount; // To keep the count of group files
    }
 ```

 ##### FUNCTIONS

 function getFileInfo => This function is used to get the file info of an ethereum identity number, this is more intrinsic
 ``` solidity
    * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @return protocol returns the protocol used for storage of the file (0 - URL, 1 - IPFS)
     * @return protocolMeta returns the meta info associated with a protocol
     * @return fileName is the name of the file
     * @return fileHash is the Hash of the file
     * @return hashExtraInfo is extra info stored as part of the protocol used 
     * @return hashFunction is the function used to store that hash
     * @return hashSize is the size of the digest
     * @return encryptedStatus indicates if the file is encrypted or not 
 ```

  function getFileInfo => This function is used to get the file info

  ``` solidity
   * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @return timestamp indicates the timestamp of the file
     * @return associatedGroupIndex indicates the group which the file is associated to in the user's FMS
     * @return associatedGroupFileIndex indicates the file index within the group of the user's FMS
  ```

    function getFileTransferInfo => This function is used to get  to get file tranfer info of an EIN

    ``` solidity
     * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @return transferCount indicates the number of times the file has been transferred
     * @return transferEIN indicates the EIN of the user to which the file is currently scheduled for transfer
     * @return transferIndex indicates the transfer index of the target EIN where the file is currently mapped to
     * @return markedForTransfer indicates if the file is marked for transfer or not
    ```
function getFileTransferOwners =>This function is used to get the transfer OWNER info of an EIN

``` solidity
 * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _transferIndex is index to poll which is useful to get the history of transfers and to what EIN the file previously belonged to
     * @return previousOwnerEIN is the EIN of the user who had originally owned that file
```
function createFileObject =>This function to create a basic File Object for a given file
``` solidity
   * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _protocolMeta is the meta info which is stored for a certain protocol
     * @return _groupIndex is the index of the group where the file is stored
     * @return _groupFilesCount is the number of files stored in that group 
```
function createFileMetaObject => This function to create a File Meta Object and attach it to File Struct (IceFMS Library)

``` solidity
   * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _protocol is type of protocol used to store that file (0 - URL, 1- IPFS)
     * @param _name is the name of the file with the extension
     * @param _hash is the hash of the file (useful for IPFS and to verify authenticity)
     * @param _hashExtraInfo is the extra info which can be stored in a 22 byte format (if required)
     * @param _hashFunction is the function used to generate the hash
     * @param _hashSize is the size of the digest
     * @param _encrypted indicates if the file is encrypted or not  
```

function writeFile => This function to write file to a user FILE MANAGEMENT SYSTEM

``` solidity
    * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param group is the pointer to the group where the file is going to be stored for the primary user (EIN)
     * @param _groupIndex indicates the index of the group for the EIN's FMS
     * @param _userFileOrderMapping is the mapping of the user's file order using SortOrder Struct (IceSort Library)
     * @param _maxFileIndex indicates the maximum index of the files stored for the primary user (EIN)
     * @param _nextIndex indicates the next index which will store the particular file in question
     * @param _transferEin is the EIN of the user for which the file is getting written to, defaults to primary user
     * @param _encryptedHash is the encrypted hash stored incase the file is encrypted
```

function moveFileToGroup => This function moves file to another group

``` solidity
    * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _fileIndex is the file index in the user's FMS files mapping
     * @param _groupMapping is the mapping of all groups for the user's FMS
     * @param _groupOrderMapping is the mapping of the order of files in that group for the primary user's FMS
     * @param _newGroupIndex is the index of the new group where file has to be moved
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
     * @return groupFileIndex is the index of the file stored in that relevant group of the primary user 
```

function deleteFile => This function delete file of the owner by calling the internal function _deleteFIleMappings

``` solidity
 * @param _ein is the EIN of the primary user
     * @param _fileIndex is the index where file is stored
     * @param _globalItemIndividual is the Association Struct (IceGlobal Library) that contains additional info about file
     * @param _fileOrderMapping is the mapping of the files of the primary user's FMS
     * @param _fileCountMapping is the mapping of the file count of all the users
     * @param _fileGroup is the Group Struct (IceFMS Library) under which the file in question is stored
     * @param _fileGroupOrder is the SortOrder Struct (IceSort Library) which points to the order of the file in the user's group
     * @param _totalSharesMapping is the mapping of the entire shares
     * @param _totalShareOrderMapping is the mapping of the entire shares order using SortOrder Struct (IceSort Library)
     * @param _shareCountMapping is the mapping of all share count
```
function _deleteFileMappings => This function delete file mappings from a user's FMS system it is an internal function

``` solidity
 * @param _ein is the EIN of the user
     * @param _globalItemIndividual is the Association Struct (IceGlobal Library) that contains additional info about file
     * @param _totalSharesMapping is the mapping of the entire shares
     * @param _totalShareOrderMapping is the mapping of the entire shares order using SortOrder Struct (IceSort Library)
     * @param _shareCountMapping is the mapping of all share count
```
function _deleteFileObject => This function delete file mappings from a user's FMS system it is an internal function

``` solidity
 * @param _files is the mapping of all pointer to the File Struct (IceFMS Library) passed for a user's FMS
     * @param _ein is the EIN of the user
     * @param _fileIndex is the file index in the user's FMS files mapping
     * @param _fileOrderMapping is the mapping of the files of the primary user's FMS
     * @param _fileCountMapping is the mapping of the file count of all the users
     * @param _fileGroup is the Group Struct (IceFMS Library) under which the file in question is stored
```

function addFileToGroup => This function add file to a group

``` solidity
 * @param _groupIndex is the index of the group belonging to that user, 0 is reserved for root
     * @param _fileIndex is the index of the file belonging to that user
     * @return associatedGroupIndex is the group index of within the mapping of groups for the specific user
     * @return associatedGroupFileIndex is the index where the file is placed within that group in the specific user's FMS
    
```
function removeFileFromGroup => This function remove file from a group

``` solidity
 * @param self is the pointer to the relevant Group Struct (IceFMS Library) which has the file in the primary user's FMS
 * @param _groupFileOrderIndex is the index of the file order within that group
```

function remapFileToGroup => This function remap file from one group to another

``` solidity
   * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _existingFileIndex is the file index in the user's FMS files mapping
     * @param _oldGroup is the pointer to the old group where the file was present in the user's FMS
     * @param _newGroup is the pointer to the new group where the file will be moved in the user's FMS
     * @param _newGroupIndex is the index of the new group where file has to be moved
     * @return groupFileIndex is the index of the file stored in that relevant group of the primary user 
```

function condItemMarkedForTransfer => This function check that a file has been marked for transfer, it is a view function that have a require which checks if the fileMeta has been marked for transfer.

function condMarkedForTransferee => This function check that a file has been marked for transferee EIN, it is a view function that checks if the group file exists or not

```solidity
* @param _transfereeEIN is the intended EIN for file transfer
```

function condNonReservedItem => This function check that ID = 0 is not modified as it's reserved item, this function checks if the index passed is greater than 0.

```solidity
* @param _index The index to check
```

function getGroup  => This function return group info

```solidity
 * @param self is the pointer to the Group Struct (IceFMS Library) passed
     * @param _groupIndex the index of the group
     * @param _groupCount is the count of the number of groups for that specific user
     * @return index is the index of the group
     * @return name is the name associated with the group
```

function createGroup => This function create a new Group for the user

```solidity
     * @param self is the mapping of all pointer to the Group Struct (IceFMS Library) passed for a user's FMS
     * @param _ein is the EIN of the primary user
     * @param _groupName is the name which should be given to the group
     * @param _groupOrderMapping is the mapping of the Groups Struct (IceFMS Library) of the primary user's FMS
     * @param _groupCountMapping is the mapping of the file count of all the users
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
     * @param _globalIndex1 is the initial first index of global items
     * @param _globalIndex2 is the initial second index of global items
     * @return newGlobalIndex1 is the new first index of global items
     * @return newGlobalIndex2 is the new second index of global items
     * @return nextGroupIndex is the new count of the group index after creating a group
```
function _createGroupInner => This function facilitate in creating new Group for the user this is an internal function

```solidity
     * @param groups is the mapping of all pointer to the Group Struct (IceFMS Library) passed for a user's FMS
     * @param _ein is the EIN of the primary user
     * @param _groupName is the name which should be given to the group
     * @param _groupOrderMapping is the mapping of the Groups Struct (IceFMS Library) of the primary user's FMS
     * @param _groupCountMapping is the mapping of the file count of all the users
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
     * @param _globalIndex1 is the initial first index of global items
     * @param _globalIndex2 is the initial second index of global items
     * @return newGlobalIndex1 is the new first index of global items
     * @return newGlobalIndex2 is the new second index of global items
     * @return nextGroupIndex is the new count of the group index after creating a group
```
function renameGroup => This function rename an existing group for the user / ein


```solidity
    @param self is the pointer to the Group Struct (IceFMS Library) passed
     * @param _groupIndex the index of the group
     * @param _groupCount is the count of the number of groups for that specific user
     * @param _groupName describes the new name of the group 
```

function deleteGroup => This function delete an existing group for the user / ein

```solidity
     * @param self is the mapping of all pointer to the Group Struct (IceFMS Library) passed for a user's FMS
     * @param _ein is the EIN of the user
     * @param _groupIndex describes the associated index of the group for the user / ein
     * @param _groupOrderMapping is the mapping of the Groups Struct (IceFMS Library) of the primary user's FMS
     * @param _groupCountMapping is the mapping of the file count of all the users
     * @param _totalSharesMapping is the mapping of the entire shares
     * @param _totalShareOrderMapping is the mapping of the entire shares order using SortOrder Struct (IceSort Library)
     * @param _shareCountMapping is the mapping of all share count
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```
function condGroupEmpty => This function check that Group Order is valid

```solidity
     * @param self is the particular group in question
```

function condNonReservedGroup => This function check that index 0 is not modified as this is Reserved in the Group Struct (IceFMS Library) for root folder

```solidity
    * @param _index The index to check
```

 function doInitiateFileTransferChecks => This function check file transfer conditions before initiating a file transfer 

 
```solidity
   * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfererEIN is the EIN which is transferring the file 
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _fileIndex is the index where the file is stored with respect to the user who is transferring the file
     * @param _fileCountMapping is the mapping of the file count of all the users
     * @param _totalGroupsMapping is the entire mapping of the groups of the Ice FMS
     * @param _blacklist is the entire mapping of the Blacklist for all users
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
     * @param _identityRegistry is the pointer to the ERC-1484 Identity Registry
```

function doFileTransferPart1 => This function does file transfer(Part 1) from previous (current) owner to new owner

```solidity
   * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfererEIN is the previous(current) owner EIN
     * @param _transfereeEIN is the EIN of the user to whom the file needs to be transferred
     * @param _fileIndex is the index where file is stored
     * @param _totalFilesOrderMapping is the entire mapping of the files order of the Ice FMS
     * @param _fileCountMapping is the mapping of the file count of all the users
     * @return nextTransfereeIndex is the index number where the file is stored in the recipient user's File Struct mapping 
```


function doFileTransferPart2 => This function does file transfer(Part 2) from previous (current) owner to new owner

```solidity
  * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfereeEIN is the EIN of the user to whom the file needs to be transferred
     * @param _fileIndex is the index where file is stored
     * @param _toRecipientGroup is the intended group of the recipient user where the file should go
     * @param _recipientGroupCount is the number of groups present in the recipient's user FMS system
     * @param _nextTransfereeIndex is the index number where the file is stored in the recipient user's File Struct mapping
     * @param _totalGroupsMapping is the entire mapping of all the groups in Ice FMS
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```

 function doPermissionedFileTransfer => This function initiate requested file transfer in a permissioned manner

 ```solidity
   * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _transfers is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```

function _initiatePermissionedFileTransfer => This function initiate requested file transfer, this is an internal function


 ```solidity
     * @param self is the pointer to the File Struct (IceFMS Library) passed
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _transfers is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```
function acceptFileTransferPart1 => This function accept file transfer (part 1) from a user

```solidity
     * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfererEIN is the previous(current) owner EIN
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _fileIndex is the index where file is stored
```

function acceptFileTransferPart2 => This function accept file transfer (part 2) from a user

```solidity
  * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _transferSpecificIndex is the index of the transfer mapping of the recipient which contains transfer info for that file
     * @param _transfersMapping is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```
function cancelFileTransfer => This function cancel file transfer inititated by the current owner and / or recipient

```solidity
    * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfererEIN is the previous(current) owner EIN
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _fileIndex is the index where file is stored
     * @param _transfersMapping is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```
 function _cancelFileTransfer => This function ancel file transfer inititated by the current owner or the recipient, this is a private function

 ```solidity
       * @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfererEIN is the previous(current) owner EIN
     * @param _transfereeEIN is the EIN to which the file will be transferred
     * @param _fileIndex is the index where file is stored
     * @param _transfersMapping is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```

function removeFileFromTransfereeMapping => This function remove file from Transfers mapping of Transferee after file is transferred to them, this is an internal function

 ```solidity
* @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _transfereeEIN is the new owner EIN
     * @param _transferSpecificIndex is the index of the association mapping of transfers
     * @param _transfersMapping is the mapping of all transfers for the transferee's FMS
     * @param _transferOrderMapping is the mapping of the order of transfers for the transferee user's FMS
     * @param _transferCountMapping is the mapping of the entire transfer count for every user
     * @param _globalItems is the mapping of all items stored by all users in the Ice FMS
```
function stringToBytes32 => This function convert string to bytes32 format

 ```solidity
* @param self is the entire mapping of all the pointers to the File Struct (IceFMS Library)
     * @param _source is the string which needs to be converted
     * @return result is the bytes32 representation of that string
```

function bytes32ToString => This function convert bytes32 to string format

```solidity
    @param _x is the bytes32 format which needs to be converted
     * @return result is the string representation of that bytes32 string
```



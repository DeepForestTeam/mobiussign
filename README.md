# Möbius Sign™
 *Möbius Sign™* is a up-to-date technology of cryptographic security from information changing which corresponding to BlockChain, allows any wishful to ensure in the integrality of not only single document, fact and docchain, but also of the whole system information in general

## MobiusSign API
The service is generally a storage of consistent hashes linked to time marks which ensures integrity and continuity of the signatures chain. The storage is organized in the way which avoids any ability of modification or removal of the chain links without affecting all next links.

Service consists of two main parts:

- **MobiusTime** - mechanism of creating unique time marks
- **MobiusSign** - mechanism of integral continuous signs chain

**Attention:**
*You should always remember that all information gathered by **MobiusSign™** is considered as public data.*

If you intend to sign any confidential data you should use one of the following methods:

- You can skip the DataBlock and pass only DataHash. This way you can confirm the data authenticity without making it public. The data itself should be stored on the side of the service which requested the signature. This way you can also avoid the data size limit. If you create DataHash by yourself we recommend appending MobiusTime to your data to ensure the signing time on your side.
- You can send your pre-encrypted information to DataBlock. The encryption should be made with any resistant algorithm without passing keys to our service. This data volume is limited to 32Kb.

###API

####API Endpoint
All API URLs listed are relative to ```http://mobiussign.com/```. For example, the ```/api/sign/``` call is reachable at ```http://mobiussign.com/api/sign/```.

####Response format
Responses to all requests are always in [JSON](https://en.wikipedia.org/wiki/JSON) format.

####API Requests
Getting current **MobiusTime**:
```
/api/time
```
Can be requested using `GET` method. Returns the details of current MobiusTime mark.

Example request:
```
http://mobiussign.com/api/time
```

Example Response:

```
{
  "result": "OK",
  "time_zone": "UTC",
  "time": "2016-08-10 13:17:41",
  "unix_time": 1470835061,
  "salt_hash": "C9415DDCF03E52598D8FFBFFFE90844E1CD3ED33CE600B9E2E8BE3632FE8CFD4",
  "pepper_hash": "FB4179D2EE768092",
  "mobius_time": "C808B0E2D0693C7ED68F8E65E470562EED1F581B49A3A69FA5E9D84B57B6DB93",
  "rsa_time": "n/a"
}
```

#####Check Mobius Time value
```
/api/time/{mobius_time}
```
Can be requested using GET method. The {mobius_time} is a MobiusTime mark hash.
Validates and returns the details of provided MobiusTime mark details.
The fields returned are the same as in the ‘/api/time’ request.
Example request:

```
http://mobiussign.com/api/time/51BB0B1727BC49FD60459518CCAF01EF00A149CE7F5F368CFB778F27EB80A136
```

Example Response:

```
{
  "result": "OK",
  "time_zone": "UTC",
  "time": "2016-08-10 13:17:41",
  "unix_time": 1470835061,
  "salt_hash": "C9415DDCF03E52598D8FFBFFFE90844E1CD3ED33CE600B9E2E8BE3632FE8CFD4",
  "pepper_hash": "FB4179D2EE768092",
  "mobius_time": "C808B0E2D0693C7ED68F8E65E470562EED1F581B49A3A69FA5E9D84B57B6DB93",
  "rsa_time": "n/a"
}
```
In case of error:

```
{
  "result": "Hash not found",
  "note": "",
  "result_code": 404
}
```

###Algorithms

####MobiusTime™
The time mark (time hash) is an sha512.Sum512_256 encoded **DataBlock**.
**DataBlock** consists of merged fields:```SaltHash``` , ```SignPepper```, ```UnixTimeStampBytes``` and ```BaseSalt```.

```
DataBlock:=([SaltHash][SignPepper][UnixTimeStampBytes][BaseSalt])
```
Where:

- ```SignPepper``` - random 8 bytes in hex string representation
- ```BaseSalt``` - basic salt string for the whole project
- ```SaltHash``` - previous time mark hash (for the first mark it has a BaseSalt value)
- ```UnixTimeStampBytes``` - int64 value of the current UnixTimeStamp

**MobiusTime** value example: ```A7532AEFD875EB4A159A1C02DFF3DE59DF6CE72F592B192543C01F0493F4AEB2```

####MobiusSign™
The ***MobiusSign*** is a simple SHA2 512 value of given DataBlock which consists of several merged values.

```
DataBlock:=([SaltHash][Pepper][ServiceID][ObjectID][ConsumerID][DataHash][MobiusTime])
```
Where: 

- ```SaltHash``` - previous element in sequence (previous MobiusSign generated by the system)
- ```Pepper``` - random 8 bytes in hex string representation
- ```ServiceID``` [optional] - unique ID of the MobiusSign™ customer
- ```ObjectID``` [optional] - field provided by MobiusSign™ customer with ID of customer’s object
- ```ConsumerID``` [optional] - field provided by MobiusSign™ customer with ID of customer’s consumer
- ```DataHash``` - hash of user-provided data
- ```MobiusTime``` - current value of MobiusTime

```DataHash``` can be provided directly as a hash or by data itself (in this case MobiusSign™ will create hash with SHA2 512 algorithm itself).

#### Used packets:
* https://github.com/op/go-logging
* https://github.com/boltdb/bolt

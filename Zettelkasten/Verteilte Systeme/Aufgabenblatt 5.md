
Erstellt: 06.05.2025 13:01

Tags:[[Verteilte Systeme]], [[Verteilte Systeme Praktikum]]

# Aufgabenblatt 5


![[VS_SoSe25_Blatt_05_Aufgabe.pdf]]

# pub_sub_server.cc

## readHashes()

Bereitet alle validen Hashes und user vor.
```java
void readHashes() {
      std::ifstream infile("Hashing/hashes.txt");
      std::string user, hash;
      int countHashesRead = 0;
      if (infile.is_open()) {
          while (infile >> user >> hash) {
              hashes.insert({user, hash});
              std::cout << user << " + " << hash << std::endl;
              ++countHashesRead;
          }
      }
      std::cout << "Count hashes read: " << countHashesRead << std::endl;
  }
```

## checkSession()

vergleicht den Hash, der in der hashes.txt mit dem Hash, der generiert wurde.

```java
bool checkSession(const pubsub::PubSubParam *param, bool checkUserName) {
      std::string userName = "";
      if (checkUserName) {
          userName = param->optmessage().message();
          std::cout << "UserName: " << userName << std::endl;
      }
      int sessionID = param->sid().id();
      std::string hashToCompare = param->hash_string();
      std::string userHash = hashes.at(sessions.at(sessionID));
      std::string generatedHash = generateHash(sessionID, userName, userHash);
      return hashToCompare == generatedHash;
  }
```

## generateHash()

Generiert einen Hash. Der ist wichtig für die Methode `checkSession()`
```java
std::string generateHash(int sessionId, std::string userName, std::string userHash) {
      std::string data = std::to_string(sessionId) + ";" + userName + ";" + userHash;
      unsigned char hash[SHA256_DIGEST_LENGTH];
      SHA256_CTX sha256;
      SHA256_Init(&sha256);
      SHA256_Update(&sha256, data.c_str(), data.size());
      SHA256_Final(hash, &sha256);
      std::stringstream ss;
      for (int i = 0; i < SHA256_DIGEST_LENGTH; ++i) {
          ss << std::hex << std::setw(2) << std::setfill('0') << (int) hash[i];
      }
      return ss.str();
  }
```

## get_session()

check, ob der UserName existiert. Dann guckt er, ob eine Session unter dem UserName existiert. Wenn alles in Ordnung ist, dann füge die Session in die map `sessions`ein.

```java
Status get_session(ServerContext *context, const pubsub::UserName *userName, pubsub::SessionId *reply) {
      std::cout << "get_session() called" << std::endl;
      std::string userNameString = userName->name();
      if (hashes.count(userNameString) == 0) {
          std::cout << "No hash for user: " << userNameString << std::endl;
          reply->set_id(-1);
          return Status(grpc::StatusCode::CANCELLED, "No hash for user " + userNameString);
      }
      int sessionId = findSessionIdByUser(userNameString);
      if (sessionId != -1) {
          std::cout << "User already has an open session" << std::endl;
          // return Status(grpc::StatusCode::ALREADY_EXISTS, "User already requested session");
          return Status::OK;
      }
      sessionId = generateSessionID();
      sessions.insert({sessionId, userNameString});
      std::cout << "Inserted <" << sessionId << ", " << userNameString << ">" << std::endl;
      reply->set_id(sessionId);
      return Status::OK;
  }
```

## validate()


```java
Status validate(ServerContext *context, const pubsub::PubSubParam *param, ReturnCode *reply) {
      std::cout << "validate() called" << std::endl;
      int sessionId = param->sid().id();
      if (sessions.count(sessionId) == 0) {
          std::cout << "No such session: " << sessionId << std::endl;
          reply->set_value(pubsub::ReturnCode_Values_SESSION_INVALID);
          return Status(grpc::StatusCode::NOT_FOUND, "No such session");
      }
      if (checkSession(param, true)) {
          std::cout << "Validate OK" << std::endl;
          validSessions.insert(sessionId);
          std::cout << "Validated session: " << sessionId << std::endl;
          reply->set_value(pubsub::ReturnCode_Values_OK);
          return Status::OK;
      } else {
          std::cout << "Validate not OK" << std::endl;
          reply->set_value(pubsub::ReturnCode_Values_WRONG_HASH_FOR_SESSION);
          return Status(grpc::StatusCode::PERMISSION_DENIED, "Hash could not be verified");
      }
  }
```

## invalidate()

löscht die Einträge aus `sessions` und `validSessions` mit der sessionID 

```java
Status invalidate(ServerContext *context, const pubsub::SessionId *param, ReturnCode *reply) {
      std::cout << "invalidate() called" << std::endl;
      int sessionId = param->id();
      if (validSessions.count(sessionId) == 0) {
          std::cout << "Session is not validated: " << sessionId << std::endl;
          reply->set_value(pubsub::ReturnCode_Values_SESSION_INVALID);
          return Status(grpc::StatusCode::UNAUTHENTICATED, "Session has not been validated");
      }
      validSessions.erase(sessionId);
      sessions.erase(sessionId);
      std::cout << "Invalidated session: " << sessionId << std::endl;
      return Status::OK;
  }
```
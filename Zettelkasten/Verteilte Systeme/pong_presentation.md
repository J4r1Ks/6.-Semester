# Verteilte Systeme im Pong-Projekt

## 1. Architektur-Überblick

Das Projekt implementiert ein **verteiltes Multiplayer-Pong-Spiel** mit:
- **Server-Client-Architektur** über WebSockets
- **Zentralisierte Spiellogik** auf dem Server
- **Dezentralisierte Client-Steuerung**

## 2. Schlüsselkomponenten für Verteilte Systeme

### WebSocket-Server (`PongServer.java`)
```java
@ServerEndpoint(value = "/quong")
public class PongServer {
    private static final List<Session> sessions = new ArrayList<>();
    
    @OnMessage
    public void onMessage(String message, Session session) {
        // Broadcast an alle verbundenen Clients
        for(Session s : sessions){
            s.getBasicRemote().sendText(message);
        }
    }
}
```

**Relevanz für VS:**
- **Zentrale Koordination** aller Spieler
- **Broadcast-Kommunikation** für Synchronisation
- **Session-Management** für Client-Verbindungen

### WebSocket-Client (`PongClient.java`)
```java
@ClientEndpoint
public class PongClient {
    @OnMessage
    public void onMessage(String message, Session session) {
        JSONObject json = new JSONObject(message);
        if(json.getString("type").equals("startGame")) {
            // Spiel mit verteilten Parametern starten
            game = new Game(json.getInt("numberOfPlayers"), json.getInt("playerID"));
        }
    }
}
```

**Relevanz für VS:**
- **Dezentrale Spielinstanzen** auf verschiedenen Rechnern
- **Eindeutige Player-IDs** für Koordination
- **Event-basierte Kommunikation**

## 3. Synchronisationsmechanismen

### Spielzustand-Synchronisation (`GameEngine.java`)
```java
public void start(GameChat gameChat, Session session){
    while(winner()){
        if(System.currentTimeMillis()-timer > 66 && !paused){
            // Nur Player 0 berechnet Ball-Physik
            if(choosePlayer == 0) ballMovement();
            
            // Sende Spielzustand an alle
            JSONObject gameData = getGameData();
            session.getBasicRemote().sendText(gameData.toString());
        }
    }
}
```

**Master-Slave-Prinzip:**
- **Player 0** = Master (berechnet Ball-Position)
- **Andere Player** = Slaves (empfangen Updates)

### Datenverteilung
```java
private JSONObject getGameData() {
    JSONObject gameData = new JSONObject();
    if(choosePlayer == 0){
        // Master sendet Ball-Daten
        gameData.put("ballX", ballX);
        gameData.put("ballY", ballY);
    }
    // Jeder Player sendet seine Position
    gameData.put("player" + (choosePlayer+1) + "Pos", playerPosition);
    return gameData;
}
```

## 4. Kommunikationspatterns

### 1. **Publish-Subscribe** (WebSocket Server)
- Server als **Message Broker**
- Clients **subscriben** zu Game-Updates
- **Broadcast** von Spielzustand-Änderungen

### 2. **Request-Response** (Chat-System)
```java
@PostMapping("/api/chat/lobby/send")
public ResponseEntity<Map<String, Object>> sendLobbyMessage(@RequestBody Map<String, String> request) {
    ChatMessage chatMessage = chatService.sendLobbyMessage(username, message);
    // Response mit Bestätigung
    return ResponseEntity.ok(response);
}
```

### 3. **Long-Polling** (Real-time Chat)
```java
@GetMapping("/lobby/longpoll")
public DeferredResult<ResponseEntity<Map<String, Object>>> longPoll(@RequestParam String since) {
    // Warte auf neue Nachrichten oder Timeout
    return chatService.registerLongPoll(sinceTime);
}
```

## 5. Herausforderungen Verteilter Systeme

### **Konsistenz**
- **Problem:** Ball-Position muss bei allen Clients identisch sein
- **Lösung:** Zentralisierte Berechnung durch Master-Client

### **Latenz**
- **Problem:** Netzwerk-Delays zwischen Eingabe und Darstellung
- **Lösung:** 66ms Update-Intervall (15 FPS) für Toleranz

### **Fehlerbehandlung**
```java
@OnError
public void onError(Session session, Throwable error) {
    error.printStackTrace();
    sendError(session, "Internal Error: " + error.getMessage());
}
```

### **Session-Management**
```java
@OnClose
public void onClose(Session session) {
    synchronized (sessions) {
        sessions.remove(session);  // Cleanup bei Disconnect
    }
}
```

## 6. Technische Highlights

### **Concurrency Control**
```java
private final List<WaitingClient> WAITING_CLIENTS = new CopyOnWriteArrayList<>();
```
- **Thread-sichere** Datenstrukturen für Multi-Client-Zugriff

### **State Replication**
- **Spielzustand** wird periodisch an alle Clients repliziert
- **Partial Updates** nur für geänderte Werte

### **Load Distribution**
- **Client-seitige Rendering** entlastet Server
- **Server** nur für Logik und Koordination

## 7. REST vs. WebSockets - Zwei Kommunikationsparadigmen

### **REST (Representational State Transfer)**
```java
@RestController
@RequestMapping("/api/chat")
public class ChatController {
    @PostMapping("/lobby/send")
    public ResponseEntity<Map<String, Object>> sendLobbyMessage(@RequestBody Map<String, String> request) {
        // Klassisches Request-Response Pattern
        ChatMessage chatMessage = chatService.sendLobbyMessage(username, message);
        return ResponseEntity.ok(response);
    }
}
```

**Eigenschaften von REST:**
- **Stateless** - jede Anfrage enthält alle nötigen Informationen
- **HTTP-basiert** - GET, POST, PUT, DELETE Verben
- **Request-Response** - Client fragt, Server antwortet
- **Ideal für:** CRUD-Operationen, API-Calls, traditionelle Web-Services

### **WebSockets**
```java
@ServerEndpoint(value = "/quong")
public class PongServer {
    @OnMessage
    public void onMessage(String message, Session session) {
        // Bidirektionale, persistente Verbindung
        for(Session s : sessions){
            s.getBasicRemote().sendText(message);
        }
    }
}
```

**Eigenschaften von WebSockets:**
- **Persistent Connection** - Verbindung bleibt offen
- **Bidirektional** - Client und Server können jederzeit senden
- **Low-Latency** - kein HTTP-Overhead bei jeder Nachricht
- **Ideal für:** Real-time Games, Live-Chat, Push-Notifications

### **Warum beide im Projekt?**
| Anwendungsfall | Technologie | Begründung |
|---|---|---|
| Chat-Nachrichten senden | REST | Einmalige Aktion mit Bestätigung |
| Chat-Updates empfangen | Long-Polling | Pseudo-Real-time ohne WebSocket |
| Game-Synchronisation | WebSocket | Echtzeitübertragung von Spielzustand |
| Login/Registrierung | REST | Klassische Formulardaten |

## 8. Warum Spring Framework?

### **Dependency Injection - Automatische Verdrahtung**
```java
@Service
public class ChatService {
    private final ChatRepository chatRepository;
    
    // Spring injiziert automatisch die Abhängigkeit
    public ChatService(ChatRepository chatRepository) {
        this.chatRepository = chatRepository;
    }
}
```

**Vorteile:**
- **Lose Kopplung** - Services kennen nur Interfaces
- **Testbarkeit** - Mock-Objekte einfach injizierbar
- **Konfiguration** - Keine manuelle Objekterstellung

### **Automatische HTTP-Mapping**
```java
@RestController
@RequestMapping("/api/chat")
public class ChatController {
    @PostMapping("/lobby/send")  // Spring mapped automatisch POST /api/chat/lobby/send
    @GetMapping("/lobby/messages")  // GET /api/chat/lobby/messages
}
```

### **Security Integration**
```java
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/chat/**").authenticated()  // Automatischer Schutz
                .requestMatchers("/login", "/register").permitAll()
            );
    }
}
```

### **JPA/Hibernate Integration**
```java
public interface ChatRepository extends JpaRepository<ChatMessage, Long> {
    @Query("SELECT c FROM ChatMessage c WHERE c.type = 'LOBBY' ORDER BY c.timestamp DESC")
    List<ChatMessage> findLobbyMessagesOrderByTimestampDesc(Pageable pageable);
}
```

**Spring erspart:**
- Manuelle JDBC-Verbindungen
- SQL-Boilerplate-Code  
- Transaction-Management
- HTTP-Request-Parsing

## 9. Login-Verfahren im Detail

### **1. Passwort-Hashing mit Pepper**
```java
@Component
public class PepperAwarePasswordEncoder implements PasswordEncoder {
    private final String pepper;  // Server-seitiges Geheimnis
    
    private String applyPepper(CharSequence raw) {
        return raw == null ? null : raw.toString() + pepper;  // Passwort + Pepper
    }
    
    @Override
    public String encode(CharSequence rawPassword) {
        return bcrypt.encode(applyPepper(rawPassword));  // BCrypt(Passwort + Pepper)
    }
}
```

**Sicherheitskonzept:**
- **Salt** (in BCrypt integriert) - verhindert Rainbow-Tables
- **Pepper** (server-seitig) - zusätzlicher Schutz bei DB-Kompromittierung
- **BCrypt** - adaptive Hashing-Funktion (rechenintensiv)

### **2. Registrierung**
```java
@PostMapping("/register")
public String registerSubmit(@ModelAttribute User user, Model model) {
    if (userRepository.findByUsername(user.getUsername()).isPresent()) {
        return "register";  // Username bereits vergeben
    }
    user.setPassword(passwordEncoder.encode(user.getPassword()));  // Hash speichern
    userRepository.save(user);
    return "redirect:/login?registered";
}
```

**Ablauf:**
1. Username-Eindeutigkeit prüfen
2. Passwort hashen (mit Pepper + BCrypt)
3. User in Datenbank speichern
4. Weiterleitung zum Login

### **3. Authentication Provider**
```java
@Bean
public DaoAuthenticationProvider authProvider() {
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService);  // Lädt User aus DB
    provider.setPasswordEncoder(passwordEncoder);        // Verifiziert Passwort
    return provider;
}
```

### **4. Login-Prozess**
```java
.formLogin(form -> form
    .loginPage("/login")                    // Custom Login-Seite
    .loginProcessingUrl("/perform_login")   // POST-Endpoint für Credentials
    .defaultSuccessUrl("/lobby", true)      // Nach erfolgreichem Login
    .failureUrl("/login?error")             // Bei fehlgeschlagener Auth
)
```

**Schritt-für-Schritt:**
1. **Eingabe:** Username + Passwort im Formular
2. **Lookup:** `UserDetailsServiceImpl` lädt User aus Datenbank
3. **Verifikation:** `PepperAwarePasswordEncoder` prüft Passwort:
   ```java
   public boolean matches(CharSequence rawPassword, String encodedPassword) {
       return bcrypt.matches(applyPepper(rawPassword), encodedPassword);
   }
   ```
4. **Session:** Spring Security erstellt authenticated Session
5. **Redirect:** Weiterleitung zur Lobby

### **5. Authorization - Zugriffskontrolle**
```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/chat/**").authenticated()  // Nur eingeloggte User
    .requestMatchers("/login", "/register").permitAll()  // Öffentlich
    .anyRequest().authenticated()  // Default: Auth erforderlich
)
```

### **6. CSRF-Schutz**
```java
.csrf(csrf -> csrf
    .ignoringRequestMatchers("/api/chat/**")  // REST APIs ausgenommen
    .ignoringRequestMatchers("/quong/**")     // WebSocket ausgenommen
)
```

**In JSP:**
```html
<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
```

## 10. Fazit - Verteilte Systeme Konzepte

Das Projekt demonstriert:
1. **Hybrid-Kommunikation:** REST für statische Ops, WebSocket für Real-time
2. **Spring-Integration:** DI, Security, JPA für Enterprise-Grade Architektur  
3. **Sichere Authentifizierung:** Pepper + BCrypt + Session-Management
4. **Master-Slave-Synchronisation** für Game State
5. **Broadcast-Pattern** für Multiplayer-Updates
6. **Session-basiertes** Connection Management

**Kernprinzip:** Moderne Web-Architektur mit zentralisierter Koordination und dezentraler Ausführung
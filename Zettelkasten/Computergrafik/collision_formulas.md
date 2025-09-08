# Collision Detection - Umfassende mathematische Analyse

## Grundlagen der Kollisionserkennung

### Koordinatensysteme und Vektoralgebra

In 3D-Grafik verwenden wir hauptsächlich **kartesische Koordinatensysteme** mit drei orthogonalen Achsen (X, Y, Z). Jeder Punkt im Raum wird durch einen **Positionsvektor** `P = (x, y, z)` beschrieben.

**Grundlegende Vektoroperationen:**
```
Skalarprodukt (Dot Product):
a · b = |a| × |b| × cos(θ) = ax×bx + ay×by + az×bz

Kreuzprodukt (Cross Product):
a × b = (ay×bz - az×by, az×bx - ax×bz, ax×by - ay×bx)

Vektorlänge:
|v| = √(vx² + vy² + vz²)
```

---

## 1. AABB (Axis-Aligned Bounding Box) Kollision - Detailanalyse

### Mathematische Grundlage

Eine AABB wird durch zwei Punkte definiert:
- **Min**: (minX, minY, minZ) - untere linke hintere Ecke
- **Max**: (maxX, maxY, maxZ) - obere rechte vordere Ecke

### Der Separating Axis Theorem (SAT)

**Grundprinzip:** Zwei konvexe Objekte kollidieren NICHT, wenn es eine Achse gibt, auf die projiziert beide Objekte getrennt sind.

Für AABBs sind die relevanten Achsen die drei Koordinatenachsen (X, Y, Z).

### Detaillierte Formel-Ableitung:

```
AABB₁: [min₁, max₁]
AABB₂: [min₂, max₂]

Für jede Achse i ∈ {X, Y, Z}:
Überlappung = min(max₁ᵢ, max₂ᵢ) - max(min₁ᵢ, min₂ᵢ)

Kollision ⟺ Überlappung > 0 auf ALLEN drei Achsen
```

### Geometrische Interpretation:

```
     │        AABB₂
     │    ┌─────────┐
     │    │         │
┌────┼────┤         │  ← X-Achsen Überlappung
│    │    │         │
│  AABB₁  │         │
│    │    └─────────┘
└────┼─────────────────
     │
```

### Code-Implementierung Analyse:
```cpp
bool AABB::intersects(const AABB& other) const {
    return (Min.X <= other.Max.X && Max.X >= other.Min.X) &&  // X-Achse
           (Min.Y <= other.Max.Y && Max.Y >= other.Min.Y) &&  // Y-Achse  
           (Min.Z <= other.Max.Z && Max.Z >= other.Min.Z);    // Z-Achse
}
```

**Warum diese Bedingung funktioniert:**
- `Min.X <= other.Max.X`: Linke Kante von AABB₁ ist links von rechter Kante von AABB₂
- `Max.X >= other.Min.X`: Rechte Kante von AABB₁ ist rechts von linker Kante von AABB₂
- Beide Bedingungen zusammen bedeuten Überlappung auf X-Achse

---

## 2. Punkt-zu-Liniensegment Distanz (Detaillierte Herleitung)

### Problem: 
Gegeben Liniensegment AB und Punkt P, finde den nächsten Punkt auf AB zu P.

### Mathematische Herleitung:

**Schritt 1: Parametrisierung des Liniensegments**
```
Jeder Punkt auf der Linie AB kann geschrieben werden als:
Q(t) = A + t × (B - A), wobei t ∈ ℝ
```

**Schritt 2: Optimierungsproblem**
```
Minimiere: f(t) = |P - Q(t)|² = |P - A - t(B - A)|²
```

**Schritt 3: Ableitung und Nullstelle**
```
f(t) = (P - A - t(B - A)) · (P - A - t(B - A))
f(t) = |P - A|² - 2t(P - A)·(B - A) + t²|B - A|²

f'(t) = -2(P - A)·(B - A) + 2t|B - A|²

Nullstelle: f'(t) = 0
⟹ t = (P - A)·(B - A) / |B - A|²
```

**Schritt 4: Segment-Constraint**
```
t_clamped = clamp(t, 0, 1)
closest_point = A + t_clamped × (B - A)
```

### Geometrische Bedeutung von t:

```
t = 0    ⟹ Punkt = A (Startpunkt)
t = 1    ⟹ Punkt = B (Endpunkt)  
0 < t < 1 ⟹ Punkt zwischen A und B
t < 0    ⟹ Punkt vor A (clamped zu A)
t > 1    ⟹ Punkt nach B (clamped zu B)
```

### Warum Clamp auf [0,1]?
Das Clamp stellt sicher, dass wir den nächsten Punkt auf dem **Segment** (nicht der unendlichen Linie) finden.

---

## 3. Punkt-zu-AABB Distanz (Comprehensive Analysis)

### Problem:
Berechne die minimale Distanz von einem Punkt P zu einer AABB.

### Konzept: Voronoi-Regionen einer AABB

Eine AABB teilt den 3D-Raum in **27 Regionen** auf:
- **8 Eckregionen** (nächster Punkt = Ecke der Box)
- **12 Kantenregionen** (nächster Punkt = auf einer Kante)
- **6 Flächenregionen** (nächster Punkt = auf einer Fläche)
- **1 Innenregion** (Distanz = 0)

### Mathematische Formulierung:

```cpp
float squaredDistancePointAABB(const Vector& point, const AABB& box) const {
    float sqDist = 0.0f;
    
    // Für jede Koordinate separat:
    for (int i = 0; i < 3; ++i) {
        float coord = point[i];
        float min_val = box.Min[i];
        float max_val = box.Max[i];
        
        if (coord < min_val) {
            float diff = coord - min_val;
            sqDist += diff * diff;
        } else if (coord > max_val) {
            float diff = coord - max_val;  
            sqDist += diff * diff;
        }
        // Innenbereich: kein Beitrag zur Distanz
    }
    return sqDist;
}
```

### Visuelle Darstellung (2D-Schnitt):

```
      │     Flächen-    │
      │     region      │
──────┼─────────────────┼──────
 Eck- │                 │ Eck-
region│   AABB (dist=0) │region  
──────┼─────────────────┼──────
      │   Flächenregion │
      │                 │
```

### Warum quadrierte Distanz verwenden?
1. **Performance**: Vermeidet teure √-Operation
2. **Monotonie**: a² < b² ⟺ a < b (für positive Werte)
3. **Numerische Stabilität**: Weniger Rundungsfehler

---

## 4. Slab Test für Liniensegment-AABB Kollision

### Konzeptuelle Grundlage

Eine AABB kann als **Schnittmenge von 3 Slab-Paaren** betrachtet werden:
- X-Slabs: Ebenen bei x = min.X und x = max.X
- Y-Slabs: Ebenen bei y = min.Y und y = max.Y  
- Z-Slabs: Ebenen bei z = min.Z und z = max.Z

### Mathematische Herleitung

**Ray-Parametrisierung:**
```
R(t) = Origin + t × Direction, wobei t ∈ [0,1] für Segment
```

**Schnittpunkt mit Slab-Ebenen:**
```
Für X-Slab bei x = constant:
Origin.x + t × Direction.x = constant
⟹ t = (constant - Origin.x) / Direction.x
```

**Für jedes Slab-Paar berechnen wir t_min und t_max:**
```cpp
if (abs(direction.x) < epsilon) {
    // Parallel zu X-Slabs
    if (origin.x < box.min.x || origin.x > box.max.x) 
        return false; // Kein Schnitt möglich
} else {
    float t1 = (box.min.x - origin.x) / direction.x;
    float t2 = (box.max.x - origin.x) / direction.x;
    if (t1 > t2) swap(t1, t2); // t1 = t_min, t2 = t_max
    
    global_t_min = max(global_t_min, t1);
    global_t_max = min(global_t_max, t2);
}
```

### Algorithmus-Invariante:
Nach Verarbeitung aller Slabs repräsentiert [global_t_min, global_t_max] den t-Bereich, in dem das Ray **alle** Slabs schneidet.

### Final Check:
```cpp
// Kollidiert wenn sich [t_min, t_max] mit [0,1] überschneidet
return !(global_t_max < 0.0f || global_t_min > 1.0f);
```

---

## 5. Punkt-zu-Dreieck (Baryzentrische Koordinaten)

### Konzept: Baryzentrische Koordinaten

Jeder Punkt P in der Dreieck-Ebene kann geschrieben werden als:
```
P = u×A + v×B + w×C  mit u + v + w = 1
```

**Geometrische Bedeutung:**
- u, v, w sind "Gewichte" der Eckpunkte
- P liegt **innerhalb** des Dreiecks ⟺ u ≥ 0, v ≥ 0, w ≥ 0

### Voronoi-Diagramm Ansatz

Das Dreieck teilt die Ebene in verschiedene Regionen:

```
        C
       /|\
      / | \
     /  |  \    Region VC (nächster Punkt = C)
    /   |   \
   / Region |  \
  /   Face   \   \  
 A─────────────────B
    Region VA    Region VB
```

**Regionen:**
1. **VA**: Nächster Punkt = A
2. **VB**: Nächster Punkt = B  
3. **VC**: Nächster Punkt = C
4. **EAB**: Nächster Punkt auf Kante AB
5. **EBC**: Nächster Punkt auf Kante BC
6. **ECA**: Nächster Punkt auf Kante CA
7. **Face**: Nächster Punkt auf Dreiecksfläche

### Mathematische Tests (vereinfacht):

```cpp
Vector ab = b - a, ac = c - a, ap = p - a;
float d1 = ab.dot(ap);  // Projektion von AP auf AB
float d2 = ac.dot(ap);  // Projektion von AP auf AC

// Test für Vertex-Region A
if (d1 <= 0.0f && d2 <= 0.0f) return a;
```

**Geometrische Interpretation:**
- `d1 ≤ 0`: AP zeigt "rückwärts" bezüglich AB → A ist näher als B-Richtung
- `d2 ≤ 0`: AP zeigt "rückwärts" bezüglich AC → A ist näher als C-Richtung

---

## 6. Segment-zu-Segment Distanz (Detaillierte Analyse)

### Problem: 
Finde die zwei nächsten Punkte auf zwei 3D-Liniensegmenten.

### Parametrisierung:
```
S₁(s) = P₁ + s×D₁, s ∈ [0,1]
S₂(t) = P₂ + t×D₂, t ∈ [0,1]
```

### Optimierungsproblem:
```
Minimiere: f(s,t) = |S₁(s) - S₂(t)|²
```

### Mathematische Herleitung:

**Schritt 1: Ausmultiplizieren**
```
S₁(s) - S₂(t) = P₁ + s×D₁ - P₂ - t×D₂ = (P₁ - P₂) + s×D₁ - t×D₂
Sei w = P₁ - P₂

f(s,t) = |w + s×D₁ - t×D₂|²
       = (w + s×D₁ - t×D₂)·(w + s×D₁ - t×D₂)
       = w·w + 2s(w·D₁) + s²(D₁·D₁) + 2t(w·D₂) + t²(D₂·D₂) - 2st(D₁·D₂)
```

**Schritt 2: Partielle Ableitungen**
```
∂f/∂s = 2(w·D₁) + 2s(D₁·D₁) - 2t(D₁·D₂) = 0
∂f/∂t = 2(w·D₂) + 2t(D₂·D₂) - 2s(D₁·D₂) = 0
```

**Schritt 3: Lineares Gleichungssystem**
```
s(D₁·D₁) - t(D₁·D₂) = -(w·D₁)
-s(D₁·D₂) + t(D₂·D₂) = -(w·D₂)

Sei: a = D₁·D₁, b = D₁·D₂, c = -(w·D₁), e = D₂·D₂, f = -(w·D₂)

as - bt = c
-bs + et = f

Determinante: Δ = ae - b²
s = (ce - bf) / Δ  
t = (af - bc) / Δ
```

**Schritt 4: Constraint Handling**
```
s_clamped = clamp(s, 0, 1)
t_clamped = clamp(t, 0, 1)

// Nachbearbeitung falls Clamping stattfand
if (s != s_clamped) {
    // Reberechne t für fixiertes s
    t = clamp((b*s_clamped + f) / e, 0, 1);
}
```

### Sonderfälle:

1. **Parallel Segments** (Δ ≈ 0):
   - Projiziere einen Endpunkt auf das andere Segment
   
2. **Degenerate Segments** (Länge ≈ 0):
   - Behandle als Punkt-zu-Segment Problem

---

## 7. Capsule-Triangle Kollision (Umfassende Analyse)

### Was ist eine Capsule?

Eine Capsule ist mathematisch die **Minkowski-Summe** eines Liniensegments mit einer Kugel:
```
Capsule = LineSegment ⊕ Sphere(radius)
```

Geometrisch: "Alle Punkte im Abstand ≤ radius zum Liniensegment"

### Zwei-Phasen-Algorithmus

#### Phase 1: Segment-Ebene Intersection

**Dreieck-Ebene Equation:**
```
Normale: N = (B - A) × (C - A)
Ebenen-Equation: N·(X - A) = 0
```

**Segment-Ebene Schnitt:**
```
Segment: S(t) = Tip + t×(Base - Tip), t ∈ [0,1]
Einsetzen: N·(S(t) - A) = 0
⟹ N·(Tip + t×(Base - Tip) - A) = 0
⟹ N·(Tip - A) + t×N·(Base - Tip) = 0
⟹ t = -N·(Tip - A) / N·(Base - Tip)
```

**Point-in-Triangle Test:**
```cpp
Vector p = tip + t * (base - tip); // Schnittpunkt
Vector c0 = (p - a).cross(b - a);
Vector c1 = (p - b).cross(c - b);  
Vector c2 = (p - c).cross(a - c);

// Alle Kreuzprodukte zeigen in dieselbe Richtung wie Normale?
bool inside = (c0.dot(N) >= 0 && c1.dot(N) >= 0 && c2.dot(N) >= 0) ||
              (c0.dot(N) <= 0 && c1.dot(N) <= 0 && c2.dot(N) <= 0);
```

#### Phase 2: Minimale Distanz Computation

**Kandidaten für nächste Punkte:**

1. **Segment-Endpunkte zu Dreieck:**
   ```cpp
   Vector cp_tip = closestPointOnTriangle(tip, a, b, c);
   Vector cp_base = closestPointOnTriangle(base, a, b, c);
   ```

2. **Segment zu Dreieck-Kanten:**
   ```cpp
   closestPointsSegmentSegment(tip, base, a, b, c1, c2);
   closestPointsSegmentSegment(tip, base, b, c, c1, c2);
   closestPointsSegmentSegment(tip, base, c, a, c1, c2);
   ```

3. **Beste Distanz wählen:**
   ```cpp
   float min_dist = sqrt(min_squared_distance);
   if (min_dist < radius) {
       penetrationDepth = radius - min_dist;
       collisionNormal = (seg_point - tri_point).normalized();
       return true;
   }
   ```

### Warum dieser Hybrid-Ansatz?

**Analytische Lösung** (Phase 1):
- ✅ Exakt und schnell für einfache Fälle
- ❌ Funktioniert nur wenn Segment die Dreieck-Ebene schneidet

**Numerische Optimierung** (Phase 2):
- ✅ Funktioniert immer
- ✅ Behandelt alle Grenzfälle
- ❌ Rechenintensiver

---

## 8. Penetration Resolution (Physikalisch korrekt)

### Problem:
Wenn zwei Objekte kollidieren, wie separiere sie physikalisch korrekt?

### Principle of Least Action

Bei mehreren möglichen Separations-Richtungen wähle die mit **minimaler Penetration**:

```cpp
// Berechne Penetration auf allen Achsen
float penetration_x = min(cap_max.x, box_max.x) - max(cap_min.x, box_min.x);
float penetration_y = min(cap_max.y, box_max.y) - max(cap_min.y, box_min.y);
float penetration_z = min(cap_max.z, box_max.z) - max(cap_min.z, box_min.z);

// Wähle Achse mit minimaler Penetration
int min_axis = argmin(penetration_x, penetration_y, penetration_z);
```

### Separation Vector Computation

```cpp
Vector normal(0, 0, 0);
if (min_axis == 0) { // X-Achse
    float cap_center_x = (cap_min.x + cap_max.x) * 0.5f;
    float box_center_x = (box_min.x + box_max.x) * 0.5f;
    normal.x = (cap_center_x < box_center_x) ? -1.0f : 1.0f;
}
// Similar für Y und Z...

position += normal * min_penetration;
```

### Velocity Correction (Impulse-Based)

```cpp
// Entferne Geschwindigkeitskomponente in Richtung der Kollision
float velocity_along_normal = velocity.dot(normal);
if (velocity_along_normal < 0.0f) { // Bewegung in Kollisionsrichtung
    velocity = velocity - normal * velocity_along_normal;
}
```

**Physikalische Bedeutung:**
- Elastische Kollision: Reflektiere Geschwindigkeitskomponente
- Inelastische Kollision: Entferne Geschwindigkeitskomponente
- Perfekt inelastisch: `velocity = 0` (Objekte bleiben zusammen)

---

## 9. Numerische Stabilität und Optimierungen

### Floating-Point Präzision

**Epsilon-Werte für Vergleiche:**
```cpp
const float EPSILON = 1e-6f;

// Statt: if (value == 0.0f)
// Besser: if (abs(value) < EPSILON)
```

### Vermeidung von Division durch Null

```cpp
float denom = a * e - b * b;
if (abs(denom) < EPSILON) {
    // Fallback für parallele/degenerierte Segmente
    return handleDegenerateCase();
}
```

### Quadrierte Distanzen

**Warum verwenden:**
```cpp
// Statt: sqrt(dx*dx + dy*dy + dz*dz) < radius
// Besser: dx*dx + dy*dy + dz*dz < radius*radius
```

**Vorteile:**
1. **Performance**: √ ist 10x langsamer als ×
2. **Präzision**: Keine Rundungsfehler durch √
3. **Monotonie**: Vergleiche bleiben korrekt

### Spatial Partitioning für Performance

**Broad Phase Collision Detection:**
```cpp
AABB queryBox = playerQueryAABB(searchRadius);
for (auto& model : models) {
    if (!model.boundingBox().intersects(queryBox)) 
        continue; // Skip detailed collision test
    // Detailed collision testing...
}
```

**Hierarchical Bounding Volumes:**
- Erste Ebene: Grobe AABB-Tests
- Zweite Ebene: Detaillierte Geometrie-Tests
- Reduziert Komplexität von O(n²) auf O(n log n)

---

## 10. Physikalische Integration (Verlet vs. Euler)

### Standard Euler Integration:
```cpp
velocity += acceleration * deltaTime;
position += velocity * deltaTime;
```

### Verbesserte Euler Integration (im Code):
```cpp
// Position mit Beschleunigungsterm
position += velocity * deltaTime + 0.5f * acceleration * deltaTime * deltaTime;
```

**Mathematische Herleitung:**
```
Aus der Kinematik: s(t) = s₀ + v₀t + ½at²
⟹ position(t + Δt) = position(t) + velocity(t)×Δt + ½×acceleration×Δt²
```

### Warum ist das besser?

**Standard Euler:**
- ❌ Akkumuliert Fehler (besonders bei hoher Beschleunigung)
- ❌ Nicht zeitumkehrbar

**Verbesserter Euler:**
- ✅ Berücksichtigt Beschleunigung direkt in Positionsupdate  
- ✅ Stabiler für Sprung-Physik
- ✅ Energie-erhaltender

---

## 11. Koordinatentransformationen und Matrizenmathe

### Homogene Koordinaten

3D-Punkte werden als 4D-Vektoren dargestellt: `(x, y, z, 1)`
```
Transformation-Matrix (4×4):
┌─────────────────┐ ┌───┐   ┌───────────────┐
│ Rxx Rxy Rxz Tx │ │ x │ = │ x' = Rxx×x + Rxy×y + Rxz×z + Tx │
│ Ryx Ryy Ryz Ty │ │ y │   │ y' = Ryx×x + Ryy×y + Ryz×z + Ty │  
│ Rzx Rzy Rzz Tz │ │ z │   │ z' = Rzx×x + Rzy×y + Rzz×z + Tz │
│  0   0   0   1 │ │ 1 │   │ 1                             │
└─────────────────┘ └───┘   └───────────────────────────────┘
```

### AABB Transformation

```cpp
AABB AABB::transform(const Matrix& m) const {
    Vector corners[8];
    corners(corners); // Alle 8 Eckpunkte berechnen
    
    for (int i = 0; i < 8; ++i) {
        corners[i] = m * corners[i]; // Transformiere jeden Eckpunkt
    }
    
    AABB result;
    result.fromPoints(corners, 8); // Neue AABB um transformierte Punkte
    return result;
}
```

**Warum 8 Eckpunkte transformieren?**
- Rotation kann AABB vergrößern
- Neue AABB = Kleinste Box um alle transformierten Eckpunkte

---

## 12. Collision Response Strategies

### Impulse-Based Response

**Grundgleichung:**
```
J = -(1 + e) × (vrel · n) / (1/m₁ + 1/m₂)
```

Wobei:
- J = Impulsmagnitude  
- e = Restitutionskoeffizient (0 = inelastisch, 1 = elastisch)
- vrel = Relativgeschwindigkeit
- n = Kollisionsnormale
- m₁, m₂ = Massen

### Position-Based Response (im Code verwendet)

```cpp
// Separiere zuerst geometrisch
position += normal * penetrationDepth;

// Korrigiere dann Geschwindigkeit
float vn = velocity.dot(normal);
if (vn < 0.0f) { // Bewegung in Kollisionsrichtung
    velocity = velocity - normal * vn; // Entferne normale Komponente
}
```

**Vorteile Position-Based:**
- ✅ Keine Penetration nach Resolution
- ✅ Stabiler für große Zeitschritte
- ✅ Einfacher zu implementieren

**Nachteile:**
- ❌ Nicht physikalisch exakt
- ❌ Kann Energie hinzufügen/entfernen

---

## Fazit: Warum diese Algorithmen?

### Echtzeit-Anforderungen:
1. **Konstante Performance** - O(1) oder O(log n) Algorithmen bevorzugt
2. **Numerische Stabilität** - Robustheit gegenüber Rundungsfehlern
3. **Skalierbarkeit** - Funktioniert mit tausenden von Objekten

### Geometrische Robustheit:
1. **Degenerierte Fälle** - Behandlung von Punkt-/Null-Objekten
2. **Grenzfälle** - Objekte die sich gerade berühren
3. **Floating-Point Toleranz** - Epsilon-basierte Vergleiche

### Spieleentwicklung Praxis:
1. **Vorhersagbares Verhalten** - Konsistente Physik
2. **Tuneable Parameter** - Künstlerische Kontrolle über Physik
3. **Performance Budget** - <1ms für gesamte Collision Detection

Diese mathematischen Grundlagen bilden das Fundament für praktisch alle modernen 3D-Spiele und Simulationen. Die Kombination aus geometrischer Intuition und numerischer Stabilität macht diese Algorithmen sowohl elegant als auch praktisch verwendbar.
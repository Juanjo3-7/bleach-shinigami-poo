# ⚔️ Práctica Java — Clases Abstractas y Herencia
### Bleach · CFGS DAW — MEDAC

---

## 📐 Diagrama de herencia

```
         ┌──────────────┐
         │  Zanpakuto   │  (clase normal)
         └──────────────┘
                            
         ┌──────────────────────────┐
         │   <<abstract>>           │
         │       Shinigami          │
         └────────────┬─────────────┘
                      │
          ┌───────────┴────────────┐
          │                        │
┌─────────▼──────────┐  ┌──────────▼──────────┐
│  <<abstract>>       │  │  <<abstract>>        │
│  ShinigamiPuro      │  │  ShinigamiMestizo    │
└────────┬────────────┘  └──────────┬───────────┘
         │                          │
    ┌────┴─────┐              ┌──────▼──────┐
    │          │              │  Sustituto  │
┌───▼───┐ ┌───▼────┐         └─────────────┘
│Teniente│ │Capitán │
└────────┘ └────────┘
```

---

## 📁 Estructura del proyecto

```
src/
├── Zanpakuto.java
├── Shinigami.java          ← abstracta
├── ShinigamiPuro.java      ← abstracta
├── ShinigamiMestizo.java   ← abstracta
├── Sustituto.java
├── Teniente.java
├── Capitan.java
└── Main.java
```

---

## 💻 Código — Apartado A

### `Zanpakuto.java`

```java
public class Zanpakuto {

    private String nombre;
    private double anchura;
    private double altura;
    private double ataque;
    private double resistencia;
    private boolean estado; // false = shikai · true = bankai

    // Guardamos los valores originales para poder restaurarlos en shikai()
    // Justificación: el método shikai() necesita los valores del constructor,
    // por lo que hay que conservarlos aparte de los valores "actuales".
    private double ataqueOriginal;
    private double resistenciaOriginal;

    public Zanpakuto(String nombre, double anchura, double altura) {
        this.nombre   = nombre;
        this.anchura  = anchura;
        this.altura   = altura;

        // Ataque aleatorio entre 2 y 25
        this.ataque   = 2 + (Math.random() * 24);

        // Resistencia aleatoria entre 70 y 150
        this.resistencia = 70 + (Math.random() * 81);

        // Guardamos los originales
        this.ataqueOriginal      = this.ataque;
        this.resistenciaOriginal = this.resistencia;

        // Toda Zanpakuto comienza en shikai
        this.estado = false;
    }

    // Multiplica el ataque y la resistencia y pasa al estado bankai
    public void bankai(double multAtaque, double multResistencia) {
        this.ataque      = this.ataque      * multAtaque;
        this.resistencia = this.resistencia * multResistencia;
        this.estado      = true;
    }

    // Restaura los valores originales y vuelve al estado shikai
    public void shikai() {
        this.ataque      = this.ataqueOriginal;
        this.resistencia = this.resistenciaOriginal;
        this.estado      = false;
    }

    // Getters necesarios para los cálculos de pelear() en las subclases de Shinigami
    // Justificación: pelear() necesita conocer el ataque del atacante
    //                y la resistencia del defensor para calcular el daño.
    public double getAtaque()      { return ataque; }
    public double getResistencia() { return resistencia; }

    @Override
    public String toString() {
        String estadoStr = estado ? "bankai" : "shikai";
        return nombre + " [atq: " + String.format("%.2f", ataque)
             + ", res: " + String.format("%.2f", resistencia)
             + "] " + estadoStr;
    }
}
```

---

## 💻 Código — Apartado B

### `Shinigami.java` — Clase abstracta base

```java
public abstract class Shinigami {

    protected String    nombre;
    protected double    vida;
    protected String    origen;
    protected int       edad;
    protected double    poder;
    private   Zanpakuto espada; // privada según el enunciado

    public Shinigami(String nombre, int edad, String origen, Zanpakuto espada) {
        this.nombre = nombre;
        this.edad   = edad;
        this.origen = origen;
        this.espada = espada;
        this.vida   = 1500;
        this.poder  = 5 + (Math.random() * 46); // entre 5 y 50
    }

    // Métodos abstractos: cada subclase concreta los implementa a su manera
    public abstract void invocarBankai();
    public abstract void pelear(Shinigami rival);

    // Getter de espada: necesario para que las subclases puedan llamar a
    // espada.bankai() en invocarBankai() y obtener ataque/resistencia en pelear().
    // Es protected para que solo las subclases lo usen, respetando el encapsulamiento.
    protected Zanpakuto getEspada() { return espada; }

    // Getter de poder: necesario para los cálculos de daño en pelear().
    // Justificación: pelear() de una subclase necesita conocer el poder del rival,
    // que es un Shinigami genérico, por lo que hay que exponerlo con un getter.
    public double getPoder() { return poder; }

    // recibirDanio(): permite reducir la vida del defensor desde pelear() del atacante.
    // Justificación: vida es protected, pero solo es accesible directamente dentro
    // de la misma clase o subclases. Como el atacante recibe un Shinigami genérico,
    // necesitamos este método para modificar la vida del objeto rival.
    public void recibirDanio(double danio) {
        this.vida -= danio;
    }

    @Override
    public String toString() {
        return nombre + " [vida: " + String.format("%.2f", vida)
             + ", poder: " + String.format("%.2f", poder) + "]\n"
             + espada.toString();
    }
}
```

---

## 💻 Código — Apartado C

### `ShinigamiPuro.java` — Clase abstracta

```java
public abstract class ShinigamiPuro extends Shinigami {

    protected int escuadron;

    public ShinigamiPuro(String nombre, int edad, String origen,
                         Zanpakuto espada, int escuadron) {
        super(nombre, edad, origen, espada);

        // Si el escuadrón no es válido (1–13), se asigna uno aleatorio entre 1 y 13
        if (escuadron >= 1 && escuadron <= 13) {
            this.escuadron = escuadron;
        } else {
            this.escuadron = 1 + (int)(Math.random() * 13);
        }
    }

    // Método exclusivo de los shinigamis puros
    public void shunpo() {
        System.out.println(nombre + " se ha desplazado usando shunpo.");
    }
}
```

---

### `ShinigamiMestizo.java` — Clase abstracta

```java
public abstract class ShinigamiMestizo extends Shinigami {

    protected double porcentajeHollow;
    protected double porcentajeQuincy;

    public ShinigamiMestizo(String nombre, int edad, String origen, Zanpakuto espada) {
        super(nombre, edad, origen, espada);

        // Porcentaje hollow: aleatorio entre 10 y 50
        this.porcentajeHollow = 10 + (Math.random() * 41);

        // Porcentaje quincy: 50 menos el valor anterior
        this.porcentajeQuincy = 50 - this.porcentajeHollow;
    }
}
```

---

## 💻 Código — Apartado D

### `Sustituto.java`

```java
public class Sustituto extends ShinigamiMestizo {

    private String zonaAsignada;

    public Sustituto(String nombre, int edad, String origen,
                     Zanpakuto espada, String zonaAsignada) {
        super(nombre, edad, origen, espada);
        this.zonaAsignada = zonaAsignada;
    }

    public void vigilar() {
        System.out.println(nombre + " sustituto vigila " + zonaAsignada);
    }

    @Override
    public void invocarBankai() {
        // Multiplicador de ataque = porcentajeHollow / 10
        // Multiplicador de resistencia = 2
        getEspada().bankai(porcentajeHollow / 10, 2);
        System.out.println(nombre + " ha invocado su bankai");
    }

    @Override
    public void pelear(Shinigami rival) {
        double danio;

        if (rival instanceof Sustituto) {
            // |ataque atacante - resistencia defensor|
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());

        } else if (rival instanceof Teniente) {
            // |ataque atacante + poder atacante - resistencia defensor|
            danio = Math.abs(getEspada().getAtaque() + this.poder
                           - rival.getEspada().getResistencia());

        } else if (rival instanceof Capitan) {
            // |ataque atacante - resistencia defensor + poder defensor|
            danio = Math.abs(getEspada().getAtaque()
                           - rival.getEspada().getResistencia()
                           + rival.getPoder());
        } else {
            // Caso genérico por si hay más tipos
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());
        }

        rival.recibirDanio(danio);
        System.out.println(nombre + " ataca a " + rival.nombre
                         + " causando " + String.format("%.2f", danio) + " de daño.");
    }

    @Override
    public String toString() {
        return nombre + " [vida: " + String.format("%.2f", vida)
             + ", poder: " + String.format("%.2f", poder) + "] Shinigami Sustituto\n"
             + getEspada().toString();
    }
}
```

---

## 💻 Código — Apartado E

### `Teniente.java`

```java
public class Teniente extends ShinigamiPuro {

    public Teniente(String nombre, int edad, String origen,
                    Zanpakuto espada, int escuadron) {
        super(nombre, edad, origen, espada, escuadron);
    }

    @Override
    public void invocarBankai() {
        // Los tenientes no pueden invocar bankai
        System.out.println(nombre + " aún no puede invocar su bankai");
    }

    @Override
    public void pelear(Shinigami rival) {
        double danio;

        if (rival instanceof Teniente) {
            // |ataque atacante - resistencia defensor|
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());

        } else if (rival instanceof Capitan || rival instanceof Sustituto) {
            // |ataque atacante - resistencia defensor + poder defensor|
            danio = Math.abs(getEspada().getAtaque()
                           - rival.getEspada().getResistencia()
                           + rival.getPoder());
        } else {
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());
        }

        rival.recibirDanio(danio);
        System.out.println(nombre + " ataca a " + rival.nombre
                         + " causando " + String.format("%.2f", danio) + " de daño.");
    }

    @Override
    public String toString() {
        return nombre + " [vida: " + String.format("%.2f", vida)
             + ", poder: " + String.format("%.2f", poder)
             + "] Teniente del escuadrón " + escuadron + "\n"
             + getEspada().toString();
    }
}
```

---

### `Capitan.java`

```java
public class Capitan extends ShinigamiPuro {

    public Capitan(String nombre, int edad, String origen,
                   Zanpakuto espada, int escuadron) {
        super(nombre, edad, origen, espada, escuadron);
    }

    @Override
    public void invocarBankai() {
        // Multiplicador de ataque = escuadron * 1.5
        // Multiplicador de resistencia = 1.5
        getEspada().bankai(escuadron * 1.5, 1.5);
        System.out.println(nombre + " ha invocado su bankai");
    }

    @Override
    public void pelear(Shinigami rival) {
        double danio;

        if (rival instanceof Capitan) {
            // |ataque atacante - resistencia defensor|
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());

        } else if (rival instanceof Teniente) {
            // |ataque atacante + poder atacante - resistencia defensor|
            danio = Math.abs(getEspada().getAtaque() + this.poder
                           - rival.getEspada().getResistencia());

        } else if (rival instanceof Sustituto) {
            // |ataque atacante - resistencia defensor + poder defensor|
            danio = Math.abs(getEspada().getAtaque()
                           - rival.getEspada().getResistencia()
                           + rival.getPoder());
        } else {
            danio = Math.abs(getEspada().getAtaque() - rival.getEspada().getResistencia());
        }

        rival.recibirDanio(danio);
        System.out.println(nombre + " ataca a " + rival.nombre
                         + " causando " + String.format("%.2f", danio) + " de daño.");
    }

    @Override
    public String toString() {
        return nombre + " [vida: " + String.format("%.2f", vida)
             + ", poder: " + String.format("%.2f", poder)
             + "] Capitán del escuadrón " + escuadron + "\n"
             + getEspada().toString();
    }
}
```

---

## 💻 Código — Main de prueba

### `Main.java`

```java
public class Main {

    public static void main(String[] args) {

        // ── Zanpakutos ──────────────────────────────────────────────
        Zanpakuto zangetsu  = new Zanpakuto("Zangetsu",  3.5, 1.7);
        Zanpakuto senbonzak = new Zanpakuto("Senbonzakura", 2.8, 1.5);
        Zanpakuto hyorinm   = new Zanpakuto("Hyorinmaru", 3.0, 1.6);

        // ── Shinigamis ───────────────────────────────────────────────
        Sustituto ichigo  = new Sustituto("Ichigo Kurosaki", 17, "Karakura", zangetsu, "Karakura");
        Capitan   byakuya = new Capitan("Byakuya Kuchiki", 250, "Soul Society", senbonzak, 6);
        Teniente  renji   = new Teniente("Renji Abarai", 200, "Inuzuri", hyorinm, 6);

        // ── mostrarInfo ──────────────────────────────────────────────
        System.out.println("══ ESTADO INICIAL ══════════════════════\n");
        System.out.println(ichigo);
        System.out.println();
        System.out.println(byakuya);
        System.out.println();
        System.out.println(renji);
        System.out.println();

        // ── bankai ───────────────────────────────────────────────────
        System.out.println("══ BANKAI ───────────────────────────────\n");
        ichigo.invocarBankai();
        byakuya.invocarBankai();
        renji.invocarBankai();   // no puede
        System.out.println();

        // ── combate ──────────────────────────────────────────────────
        System.out.println("══ COMBATE ──────────────────────────────\n");
        ichigo.pelear(byakuya);  // Sustituto vs Capitán
        byakuya.pelear(renji);   // Capitán   vs Teniente
        renji.pelear(ichigo);    // Teniente  vs Sustituto
        System.out.println();

        // ── shunpo (solo puros) ──────────────────────────────────────
        System.out.println("══ SHUNPO ───────────────────────────────\n");
        byakuya.shunpo();
        renji.shunpo();
        System.out.println();

        // ── vigilar (solo sustituto) ─────────────────────────────────
        System.out.println("══ VIGILAR ──────────────────────────────\n");
        ichigo.vigilar();
        System.out.println();

        // ── estado final ─────────────────────────────────────────────
        System.out.println("══ ESTADO FINAL ════════════════════════\n");
        System.out.println(ichigo);
        System.out.println();
        System.out.println(byakuya);
        System.out.println();
        System.out.println(renji);
    }
}
```

---

## 📤 Ejemplo de salida

```
══ ESTADO INICIAL ══════════════════════

Ichigo Kurosaki [vida: 1500,00, poder: 32,17] Shinigami Sustituto
Zangetsu [atq: 14,53, res: 112,40] shikai

Byakuya Kuchiki [vida: 1500,00, poder: 41,88] Capitán del escuadrón 6
Senbonzakura [atq: 9,72, res: 98,35] shikai

Renji Abarai [vida: 1500,00, poder: 18,54] Teniente del escuadrón 6
Hyorinmaru [atq: 17,61, res: 105,22] shikai

══ BANKAI ───────────────────────────────

Ichigo Kurosaki ha invocado su bankai
Byakuya Kuchiki ha invocado su bankai
Renji Abarai aún no puede invocar su bankai

══ COMBATE ──────────────────────────────

Ichigo Kurosaki ataca a Byakuya Kuchiki causando 69,58 de daño.
Byakuya Kuchiki ataca a Renji Abarai causando 47,94 de daño.
Renji Abarai ataca a Ichigo Kurosaki causando 190,58 de daño.

══ SHUNPO ───────────────────────────────

Byakuya Kuchiki se ha desplazado usando shunpo.
Renji Abarai se ha desplazado usando shunpo.

══ VIGILAR ──────────────────────────────

Ichigo Kurosaki sustituto vigila Karakura

══ ESTADO FINAL ════════════════════════

Ichigo Kurosaki [vida: 1309,42, poder: 32,17] Shinigami Sustituto
Zangetsu [atq: 41,26, res: 224,80] bankai
...
```

> *Los valores numéricos variarán en cada ejecución por los aleatorios.*

---

## 🧠 Resumen de métodos extra justificados

| Método / Propiedad | Clase | Justificación |
|---|---|---|
| `ataqueOriginal`, `resistenciaOriginal` | `Zanpakuto` | `shikai()` necesita restaurar los valores del constructor, que se pierden tras `bankai()` |
| `getAtaque()`, `getResistencia()` | `Zanpakuto` | `pelear()` de las subclases necesita leer estos valores del rival (objeto externo) |
| `getEspada()` | `Shinigami` | `invocarBankai()` y `pelear()` de las subclases deben acceder a la espada, que es privada |
| `getPoder()` | `Shinigami` | `pelear()` necesita el poder del rival para calcular el daño; `poder` es `protected` pero no accesible desde otra jerarquía |
| `recibirDanio(double)` | `Shinigami` | `pelear()` del atacante debe reducir la vida del defensor (objeto externo); sin este método no se puede modificar `vida` desde fuera |

---

## 📊 Tabla de fórmulas de `pelear()`

| Atacante | Defensor | Fórmula del daño |
|---|---|---|
| Sustituto | Sustituto | `\|atqAtacante − resDefensor\|` |
| Sustituto | Teniente | `\|atqAtacante + poderAtacante − resDefensor\|` |
| Sustituto | Capitán | `\|atqAtacante − resDefensor + poderDefensor\|` |
| Teniente | Teniente | `\|atqAtacante − resDefensor\|` |
| Teniente | Capitán / Sustituto | `\|atqAtacante − resDefensor + poderDefensor\|` |
| Capitán | Capitán | `\|atqAtacante − resDefensor\|` |
| Capitán | Teniente | `\|atqAtacante + poderAtacante − resDefensor\|` |
| Capitán | Sustituto | `\|atqAtacante − resDefensor + poderDefensor\|` |

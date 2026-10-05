---
tags: [android, seguridad, encriptacion, sharedpreferences, storage, desarrollo-aplicaciones-1]
aliases: [EncryptedSharedPreferences, AndroidX Security, Tokens seguros Android]
---

# CC_EncryptedSharedPreferences

> Implementación segura de `SharedPreferences` que **cifra automáticamente** los datos guardados. Parte de la librería **AndroidX Security**.

## Cuándo usarlo

- Tokens [[JWT]] para sesiones seguras
- Credenciales temporales (**nunca** contraseñas en texto plano)
- Claves API y configuraciones de seguridad

> Para elegir entre los 4 métodos de persistencia: ver la tabla comparativa en [[Clase 04 - Inyección de Dependencias y Storage]]

> ⚠️ No confundir con [[DataStore]]: DataStore es para configuraciones normales (idioma, modo oscuro). Esto es específicamente para **datos sensibles**.

---

## Dependencia

```gradle
implementation "androidx.security:security-crypto:1.1.0-alpha06"
```

---

## Crear la instancia

```java
SharedPreferences sp = EncryptedSharedPreferences.create(
    "secure_prefs",
    MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC),
    context,
    PrefKeyEncryptionScheme.AES256_SIV,
    PrefValueEncryptionScheme.AES256_GCM
);
```

| Parámetro | Qué es |
|---|---|
| `"secure_prefs"` | Nombre del archivo de preferencias |
| `MasterKeys.getOrCreate(...)` | La clave maestra que cifra/descifra — se genera y guarda sola, en el Keystore de Android |
| `PrefKeyEncryptionScheme.AES256_SIV` | Algoritmo para cifrar las **keys** |
| `PrefValueEncryptionScheme.AES256_GCM` | Algoritmo para cifrar los **valores** |

---

## Guardar y leer

```java
// Guardar
sp.edit().putString("token", jwt).apply();

// Leer
String t = sp.getString("token", null);
```

> La API es **idéntica** a `SharedPreferences` normal — la diferencia está toda en cómo se crea la instancia. Por eso migrar código viejo es simple: cambiás la creación, el resto del código (`.edit()`, `.putString()`, `.getString()`) queda igual.

---

## Uso real: dentro de TokenManager (Hilt)

En la práctica, no se usa `EncryptedSharedPreferences` suelto — se encapsula dentro de una clase como `TokenManager`, que [[Hilt]] inyecta donde haga falta (ej: en el interceptor de OkHttp que agrega el header `Authorization`).

```java
public class TokenManager {

    private final SharedPreferences prefs;

    @Inject
    public TokenManager(@ApplicationContext Context context) {
        try {
            prefs = EncryptedSharedPreferences.create(
                    "secure_prefs",
                    MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC),
                    context,
                    PrefKeyEncryptionScheme.AES256_SIV,
                    PrefValueEncryptionScheme.AES256_GCM
            );
        } catch (Exception e) {
            throw new RuntimeException("Error creando EncryptedSharedPreferences", e);
        }
    }

    public void saveToken(String token) {
        prefs.edit().putString("token", token).apply();
    }

    public String getToken() {
        return prefs.getString("token", null);
    }

    public void clearToken() {
        prefs.edit().remove("token").apply();
    }
}
```

> Ver [[Hilt#Interceptor de OkHttp — agregar el token automáticamente]] para el flujo completo de cómo este `TokenManager` se conecta con Retrofit.

---

## Por qué no usar SharedPreferences normal para esto

| | SharedPreferences normal | EncryptedSharedPreferences |
|---|---|---|
| Datos en disco | **Texto plano**, legible con acceso root o backup | Cifrados con AES-256 |
| Riesgo con un token JWT | Cualquiera con acceso al archivo lo lee | Ilegible sin la clave del Keystore |
| Uso recomendado | Configuraciones normales | Tokens, credenciales, claves API |

---

## Buenas prácticas

- Nunca guardes la contraseña del usuario, ni siquiera cifrada — guardá el **token de sesión**
- Combinalo con expiración de token: si el JWT vence, pedí uno nuevo en vez de guardar tokens "para siempre" — ver el campo `exp` en [[JWT]]
- Para inyectarlo de forma limpia en el proyecto, se puede proveer con un `@Module` de [[Hilt]] igual que `Retrofit` o `Room`

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Clase 05 - JWT y LocalStorage]]
- [[DataStore]]
- [[Hilt]]
- [[JWT]]
- [[Retrofit - Consumo de API REST]]

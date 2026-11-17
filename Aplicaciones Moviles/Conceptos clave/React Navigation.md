---
tags: [react-native, react-navigation, navegacion, stack-navigator, desarrollo-aplicaciones-1]
aliases: [React Navigation, NavigationContainer, Stack Navigator, Tab Navigator, Drawer Navigator, useNavigation, useRoute]
---

# CC_React Navigation

> Librería **oficial y recomendada** para manejar la navegación entre pantallas en React Native. A diferencia de la web (donde existen rutas como `/home`, `/about`), **React Native no incluye navegación por defecto**.

## Los 3 tipos de navegador

| Navegador | Qué hace | Ejemplo real |
|---|---|---|
| **Stack Navigator** | Pilas de pantallas — navegar adelante/atrás | Flujo típico de cualquier app |
| **Tab Navigator** | Navegación con pestañas | Instagram (Home, Buscar, Reels, Perfil) |
| **Drawer Navigator** | Menú deslizable | Gmail (menú lateral) |

> Los tres se pueden combinar — ej: un Tab Navigator donde cada tab tiene su propio Stack Navigator adentro.

---

## Los 3 componentes obligatorios

Para que React Navigation funcione se necesitan **exactamente 3 piezas**, siempre en este orden jerárquico:

| Componente | Dónde va | Qué hace |
|---|---|---|
| `NavigationContainer` | `App.js` (raíz) | Contexto global que gestiona el estado de navegación |
| `<X>.Navigator` | `navigation/AppNavigator.jsx` | Define el tipo de navegación (Stack, Tab, Drawer) |
| `<X>.Screen` | Dentro del Navigator | Registra cada pantalla con su nombre y componente |

> ⚠️ **Sin `NavigationContainer`**, React Navigation no tiene contexto y cualquier intento de navegar lanza: `Couldn't find a navigation object`. Siempre debe ser el componente **raíz** que envuelve toda la app.

```jsx
// Jerarquía obligatoria
<NavigationContainer>
    <Stack.Navigator>
        <Stack.Screen />
        <Stack.Screen />
        ...
    </Stack.Navigator>
</NavigationContainer>
```

> Es el equivalente al `BrowserRouter` de React Router Web.

---

## NavigationContainer y AppNavigator — código completo

```jsx
// App.js
import { NavigationContainer } from '@react-navigation/native';
import AppNavigator from './navigation/AppNavigator';

export default function App() {
    return (
        <NavigationContainer>
            <AppNavigator />
        </NavigationContainer>
    );
}
```

```jsx
// navigation/AppNavigator.jsx
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

export default function AppNavigator() {
    return (
        <Stack.Navigator
            initialRouteName="Home"
            screenOptions={{
                headerStyle: { backgroundColor: colors.primary },
                headerTintColor: colors.white,
            }}>
            <Stack.Screen name="Home" component={HomeScreen} options={{ title: 'PokeExplorer' }} />
            <Stack.Screen name="PokemonList" component={PokemonListScreen} options={{ title: 'Pokedex' }} />
            <Stack.Screen
                name="PokemonDetail"
                component={PokemonDetailScreen}
                options={({ route }) => ({ title: route.params.name })}
            />
        </Stack.Navigator>
    );
}
```

| Propiedad | Qué hace |
|---|---|
| `initialRouteName` | Pantalla al abrir la app. Si se omite, usa la primera `Screen` |
| `screenOptions` | Opciones compartidas por **todas** las pantallas del Navigator |
| `options` por Screen | Sobreescribe `screenOptions`; acepta una **función** con `{ route }` para título dinámico |

---

## Paso de parámetros entre pantallas

Para enviar datos de una pantalla a otra se usa el **segundo argumento** de `navigate`. `route.params` es un objeto plano: se pueden pasar strings, números u objetos serializables.

### Quien envía

```jsx
// PokemonListScreen.jsx
export default function PokemonListScreen({ navigation }) {
    return (
        <FlatList
            data={pokemons}
            renderItem={({ item }) => (
                <TouchableOpacity
                    onPress={() =>
                        navigation.navigate('PokemonDetail', { name: item.name }) // <-- envía
                    }
                >
                    <Text>{item.name}</Text>
                </TouchableOpacity>
            )}
        />
    );
}
```

### Quien recibe

```jsx
// PokemonDetailScreen.jsx
export default function PokemonDetailScreen({ route, navigation }) {
    // Lee el parámetro enviado
    const { name } = route.params;

    return (
        <View>
            <Text>Detalle de: {name}</Text>
            <Button title="Volver" onPress={() => navigation.goBack()} />
        </View>
    );
}
```

---

## El prop navigation

React Navigation **inyecta automáticamente** el prop `navigation` a todos los componentes registrados como pantalla dentro de un Navigator (ej. `Stack.Screen`).

| Método | Qué hace |
|---|---|
| `navigation.navigate('Pantalla')` | Ir a una pantalla |
| `navigation.goBack()` | Volver atrás |
| `navigation.replace('Otra')` | Reemplazar la pantalla actual (no se apila — "atrás" no vuelve a la anterior) |
| `navigation.navigate('P', { id: 5 })` | Navegar pasando parámetros |

```jsx
function HomeScreen({ navigation }) {
    return <Button title="Ir a detalles" onPress={() => navigation.navigate('Details')} />;
}
```

---

## Hooks de navegación — useNavigation y useRoute

A veces un componente necesita navegar o leer parámetros **sin ser una pantalla registrada** (ej: un botón reutilizable dentro de `components/`). Para eso están los hooks:

### useNavigation()

Para componentes que **no son pantalla** pero necesitan navegar.

```jsx
import { useNavigation } from '@react-navigation/native';

function MiBoton() {
    const navigation = useNavigation();
    return (
        <Button title="Volver a Home" onPress={() => navigation.navigate('Home')} />
    );
}
```

### useRoute()

Para leer parámetros pasados **desde cualquier componente**, no solo desde el prop `route` de una Screen.

```jsx
import { useRoute } from '@react-navigation/native';

// Quien navega:
navigation.navigate('Detalles', { nombre: 'Juan', edad: 25 });

// DetallesScreen.js:
function DetallesScreen() {
    const route = useRoute();
    const { nombre, edad } = route.params;
}
```

---

## Resumen — Hooks & Props de React Navigation

| Hook / Prop | ¿Para qué sirve? | ¿Dónde se usa? |
|---|---|---|
| `navigation` prop | Navegar entre pantallas | En pantallas (`Stack.Screen`) |
| `route` prop | Leer parámetros pasados | En pantallas |
| `useNavigation()` | Navegar desde componentes hijos | En cualquier componente |
| `useRoute()` | Leer parámetros desde cualquier componente | En cualquier componente |
| `useFocusEffect()` | Ejecutar un efecto al enfocar la pantalla | En pantallas (avanzado) |
| `useIsFocused()` | Saber si la pantalla está visible actualmente | En pantallas o hooks |

> `useFocusEffect` es como un [[React Hooks#useEffect|useEffect]], pero se dispara cada vez que la pantalla vuelve a estar en foco (no solo al montar) — útil para refrescar datos al volver de otra pantalla.

---

## Dónde vive esto en el scaffold

```
my-app/
└── navigation/
    ├── AppNavigator.jsx     ← Stack.Navigator principal
    ├── AuthNavigator.jsx    ← Stack de login/registro
    └── TabNavigator.jsx     ← Tabs si la app los usa
```

> Ver [[React Native#Scaffold — estructura de carpetas recomendada]].

---

## Comparación con Android nativo

| Android nativo | React Navigation |
|---|---|
| [[Navigation Component]] | React Navigation |
| `NavHostFragment` | `NavigationContainer` |
| `nav_graph.xml` | `<Stack.Navigator>` + `<Stack.Screen>` |
| `Bundle` con `putString`/`getString` | `route.params` |
| `navController.navigate(actionId)` | `navigation.navigate('Pantalla')` |
| `popBackStack()` | `navigation.goBack()` |

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `Couldn't find a navigation object` | Falta `NavigationContainer` envolviendo la app, o el componente no está registrado como `Screen` |
| `route.params` es `undefined` | No se pasó el segundo argumento en `navigate()`, o se está leyendo antes de que la navegación ocurra |
| El título de la pantalla no cambia dinámicamente | `options` está como objeto fijo en vez de función `({ route }) => ({...})` |
| "Atrás" vuelve a una pantalla que no debería | Se usó `navigate()` en vez de `replace()` donde no se quería apilar |

---

## Relacionado

- [[Clase 08 - React Native vs React DOM, APIs REST y React Navigation]]
- [[React Native]]
- [[React Hooks]]
- [[Axios y fetch API]]
- [[Navigation Component]]
- [[Intents]]

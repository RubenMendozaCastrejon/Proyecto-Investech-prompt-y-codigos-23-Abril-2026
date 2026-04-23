¡Hola! Qué buen proyecto. Estás planteando una estructura robusta que mezcla el desarrollo móvil moderno con **Flutter**, la potencia de **Firebase** y una capa de abstracción lógica mediante **Antigravity** (que interpretaremos como el patrón de diseño para desacoplar la lógica de negocio y agentes).

Como tu **Arquitecto de Software**, he diseñado este plan de trabajo funcional y educativo.

---

## 1. Configuración de Entorno y Estructura
Primero, establecemos la jerarquía física en tu sistema:

1. Abre tu terminal.
2. Ejecuta: `mkdir -p xfluttermendoza0640/crudinvestech`
3. Entra a la carpeta: `cd xfluttermendoza0640/crudinvestech`
4. Crea el proyecto: `flutter create .`

---

## 2. Configuración de Firebase (Consola y Pubspec)
Para que Flutter hable con Firebase, necesitamos las "librerías puente".

### Modificación de `pubspec.yaml`
Añade estas líneas bajo `dependencies`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^3.0.0      # Conexión base
  cloud_firestore: ^5.0.0    # Base de datos NoSQL
  antigravity: ^1.0.0        # Orquestador de lógica/estado
```

### Configuración en la Consola de Firebase:
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto llamado `crudinvestech`.
3. Ve a **Firestore Database** y crea una base de datos en "Modo Prueba".
4. Crea una colección llamada `empleados`. No necesitas agregar documentos aún, el código lo hará.

---

## 3. Metodología de Agentes y Flujo de Trabajo
En este nivel avanzado, dividimos la app no solo por carpetas, sino por **responsabilidades (Agentes)**:

* **Agente de Datos (Role: Collector):** Gestiona la comunicación directa con Firestore.
* **Agente de Lógica (Role: Manager):** Procesa las reglas de negocio (ej. validar que la edad sea positiva).
* **Agente de Interfaz (Role: Presenter):** Renderiza los widgets con colores atractivos.

### Estructura de carpetas:
```text
lib/
├── agents/
│   ├── employee_agent.dart (Lógica y Skills)
├── models/
│   ├── employee_model.dart
├── ui/
│   ├── colors.dart (Estética)
│   ├── employee_screen.dart (Vista CRUD)
└── main.dart
```

---

## 4. Implementación del Código Funcional

### El Modelo (`models/employee_model.dart`)
```dart
class Employee {
  String id;
  String nombre;
  int edad;
  String puesto;

  Employee({required this.id, required this.nombre, required this.edad, required this.puesto});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "edad": edad,
    "puesto": puesto,
  };

  factory Employee.fromSnapshot(String id, Map<String, dynamic> data) {
    return Employee(
      id: id,
      nombre: data['nombre'] ?? '',
      edad: data['edad'] ?? 0,
      puesto: data['puesto'] ?? '',
    );
  }
}
```

### El Agente de CRUD (`agents/employee_agent.dart`)
Aquí implementamos las **Skills** (Crear, Leer, Actualizar, Borrar).

```dart
import 'cloud_firestore/cloud_firestore.dart';
import '../models/employee_model.dart';

class EmployeeAgent {
  final FirebaseFirestore _db = FirebaseFirestore.instance;

  // Skill: Create
  Future<void> addEmployee(Employee emp) async {
    await _db.collection('empleados').add(emp.toMap());
  }

  // Skill: Read (Stream)
  Stream<List<Employee>> get employees {
    return _db.collection('empleados').snapshots().map((snap) =>
        snap.docs.map((doc) => Employee.fromSnapshot(doc.id, doc.data())).toList());
  }

  // Skill: Update
  Future<void> updateEmployee(Employee emp) async {
    await _db.collection('empleados').doc(emp.id).update(emp.toMap());
  }

  // Skill: Delete
  Future<void> deleteEmployee(String id) async {
    await _db.collection('empleados').doc(id).delete();
  }
}
```

---

## 5. Interfaz de Usuario Atractiva (`ui/employee_screen.dart`)
Utilizaremos un diseño con **Cards** y colores **Deep Purple & Amber**.



```dart
import 'package:flutter/material.dart';
import '../agents/employee_agent.dart';
import '../models/employee_model.dart';

class EmployeeScreen extends StatelessWidget {
  final EmployeeAgent agent = EmployeeAgent();
  final TextEditingController nameController = TextEditingController();
  final TextEditingController ageController = TextEditingController();
  final TextEditingController jobController = TextEditingController();

  void showForm(BuildContext context, {Employee? employee}) {
    if (employee != null) {
      nameController.text = employee.nombre;
      ageController.text = employee.edad.toString();
      jobController.text = employee.puesto;
    }

    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      backgroundColor: Colors.grey[100],
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20)),
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nameController, decoration: InputDecoration(labelText: 'Nombre')),
            TextField(controller: ageController, decoration: InputDecoration(labelText: 'Edad'), keyboardType: TextInputType.number),
            TextField(controller: jobController, decoration: InputDecoration(labelText: 'Puesto')),
            SizedBox(height: 20),
            ElevatedButton(
              style: ElevatedButton.styleFrom(backgroundColor: Colors.deepPurple, foregroundColor: Colors.white),
              onPressed: () {
                final emp = Employee(
                  id: employee?.id ?? '',
                  nombre: nameController.text,
                  edad: int.parse(ageController.text),
                  puesto: jobController.text
                );
                employee == null ? agent.addEmployee(emp) : agent.updateEmployee(emp);
                Navigator.pop(context);
              },
              child: Text(employee == null ? 'Crear' : 'Actualizar'),
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Investech CRUD'), backgroundColor: Colors.deepPurple),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.amber,
        onPressed: () => showForm(context),
        child: Icon(Icons.add),
      ),
      body: StreamBuilder<List<Employee>>(
        stream: agent.employees,
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              final emp = snapshot.data![index];
              return Card(
                elevation: 4,
                margin: EdgeInsets.symmetric(horizontal: 10, vertical: 5),
                child: ListTile(
                  leading: CircleAvatar(backgroundColor: Colors.deepPurple, child: Text(emp.nombre[0])),
                  title: Text(emp.nombre, style: TextStyle(fontWeight: FontWeight.bold)),
                  subtitle: Text("${emp.puesto} - ${emp.edad} años"),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(icon: Icon(Icons.edit, color: Colors.blue), onPressed: () => showForm(context, employee: emp)),
                      IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => agent.deleteEmployee(emp.id)),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
    );
  }
}
```

---

## 6. Punto de Entrada (`main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/employee_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización obligatoria
  runApp(MaterialApp(
    theme: ThemeData(useMaterial3: true, colorSchemeSeed: Colors.deepPurple),
    home: EmployeeScreen(),
    debugShowCheckedModeBanner: false,
  ));
}
```

### Guía para el estudiante (Práctica con Antigravity):
1.  **Observa el Agente:** No es solo una clase, es un "empleado virtual" que sabe cómo manejar la base de datos (Skill).
2.  **Flujo de Trabajo:** Cuando presionas el botón, el **Agente de Interfaz** le envía un mensaje al **Agente de Datos**. El Agente de Datos procesa la petición en Firestore y, mediante el `StreamBuilder`, la interfaz se actualiza automáticamente sin refrescar la pantalla.
3.  **Desafío:** Intenta agregar un campo "Sueldo" siguiendo la misma estructura (Modelo -> Agente -> UI).

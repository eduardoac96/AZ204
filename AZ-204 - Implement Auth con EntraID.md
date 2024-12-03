
## Ejemplo con Entra ID

### 1. Registrar una Nueva Aplicación en Azure AD

Sigue estos pasos para registrar una nueva aplicación en Microsoft Entra ID (Azure AD):

#### 1.1 Inicia sesión en el portal de Azure
Accede a [https://portal.azure.com](https://portal.azure.com).

#### 1.2 Navega a "Microsoft Entra ID"
- En el panel de búsqueda, busca "Microsoft Entra ID".

#### 1.3 Registrar la aplicación
- En el menú **Administrar**, selecciona **Registros de aplicaciones** y luego **Nuevo registro**.
- En la página de **Registrar una aplicación**, ingresa la siguiente información:
  - **Nombre**: `az204appreg`.
  - **Tipos de cuentas admitidos**: Selecciona **Cuentas solo en este directorio organizacional**.
  - **URI de redirección (opcional)**: Selecciona **Cliente público/nativo (móvil y escritorio)** e ingresa `http://localhost` en el cuadro de texto.
- Haz clic en **Registrar**.

![AppRegistration](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image.png)

#### 1.4 Copiar las credenciales necesarias
- **ID de la aplicación (cliente)**: Se asignará automáticamente a tu aplicación.
- **ID de directorio (tenant)**: Lo encontrarás en el panel de tu aplicación registrada.

---

### 2. Configurar la App de Consola en .NET

Ahora configuraremos una aplicación de consola en .NET que se autentique utilizando MSAL para obtener un token de acceso.

#### 2.1 Crear un nuevo proyecto de consola en .NET

1. Abre **Visual Studio Code** y abre una terminal.
2. Crea una carpeta para tu proyecto y navega hasta ella:

   ```bash
   md az204-auth
   cd az204-auth
   ```

3. Crea un nuevo proyecto de consola:

   ```bash  
   dotnet new console
   ```

4. Abre la carpeta en Visual Studio Code:

   ```bash
   code . -r
   ```

#### 2.2 Agregar el paquete MSAL

En la terminal de Visual Studio Code, ejecuta el siguiente comando para agregar el paquete **Microsoft.Identity.Client**:

```bash
dotnet add package Microsoft.Identity.Client
```

---

### 3. Escribir el Código en `Program.cs`

Ahora modificaremos el archivo `Program.cs` para que la aplicación de consola pueda autenticarse contra Azure AD y obtener un token de acceso.

#### 3.1 Abre el archivo `Program.cs` y agrega las siguientes declaraciones de `using`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
```

#### 3.2 Modifica la clase `Program`

Dentro del archivo `Program.cs`, reemplaza el contenido del archivo con el siguiente código:

```csharp 
using System;
using System.Threading.Tasks;
using Microsoft.Identity.Client;

namespace az204_auth
{
    class Program
    {
        private const string _clientId = "APPLICATION_CLIENT_ID";  // Reemplaza con el Client ID de tu app registrada
        private const string _tenantId = "DIRECTORY_TENANT_ID";  // Reemplaza con el Tenant ID de tu app registrada

        public static async Task Main(string[] args)
        {
            // Crear la aplicación pública (cliente) para MSAL
            var app = PublicClientApplicationBuilder
                .Create(_clientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, _tenantId)
                .WithRedirectUri("http://localhost")
                .Build();

            // Definir los permisos requeridos
            string[] scopes = { "user.read" };  // Permiso para leer información del usuario

            // Adquirir un token interactivo
            AuthenticationResult result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

            // Mostrar el token obtenido
            Console.WriteLine($"Token:\t{result.AccessToken}");
        }
    }
}
```

---

### 4. Ejecutar la Aplicación de Consola

Para ejecutar la aplicación de consola y realizar la autenticación interactiva, sigue estos pasos:

#### 4.1 Compilar y ejecutar la aplicación

En la terminal de Visual Studio Code, ejecuta los siguientes comandos:

```bash
dotnet build  // Para verificar si hay errores de compilación
dotnet run    // Ejecutar la aplicación
```

#### 4.2 Proceso de autenticación interactiva

- Al ejecutar la aplicación, se abrirá una ventana del navegador que te pedirá que inicies sesión con una cuenta de usuario.
- Si es la primera vez que la aplicación intenta autenticarse, se te pedirá que aceptes los permisos para permitir que la aplicación lea la información del usuario (en este caso, el permiso `user.read`).
- Una vez que hayas autenticado correctamente y aceptado los permisos, la aplicación mostrará el token de acceso en la consola, algo similar a:

```bash
Token: eyJ0eXAiOiJKV1QiLCJub25jZSI6IlVhU......
```


### Resumen de Pasos

1. **Registrar la aplicación** en Azure AD.
2. **Configurar la aplicación de consola en .NET**.
3. **Escribir y ejecutar el código** para autenticarte con MSAL y obtener un token de acceso.
 
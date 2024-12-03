# Microsoft Graph en una Aplicación de Consola (.NET 8.0)
 
## 1. Registrar la Aplicación en Azure AD

Antes de continuar, asegúrate de haber registrado la aplicación en **EntraID**. Este registro proporciona el **clientId** y **tenantId** que necesitarás en el código.

## 2. Crear y Configurar la Aplicación de Consola en .NET

### 2.1. Crear el Proyecto de Consola

```bash
md az204-auth
cd az204-auth
dotnet new console
```

### 2.2. Agregar Paquetes Necesarios

Agrega los paquetes necesarios para trabajar con **Microsoft.Graph** y **Azure.Identity**, que facilitan la autenticación y la interacción con la API de Microsoft Graph:

```bash
dotnet add package Microsoft.Graph
dotnet add package Azure.Identity
```

## 3. Configurar y Modificar el Código de la Aplicación

### 3.1. Código de `Program.cs`

```csharp
        class Program
    {
        private const string _clientId = "YOUR_CLIENT_ID";  // Reemplaza con el Client ID de tu app registrada
        private const string _tenantId = "e0793d39-0939-496d-b129-198edd916feb";  // Reemplaza con el Tenant ID de tu app registrada
        private static readonly string[] Scopes = new[] { "User.Read", "Mail.Read" };
        public static async Task Main(string[] args)
        {
            // Usar DeviceCodeCredential para autenticación interactiva
            var options = new TokenCredentialOptions
            {
                AuthorityHost = AzureAuthorityHosts.AzurePublicCloud
            };

            // Callback para mostrar el código de dispositivo
            Func<DeviceCodeInfo, CancellationToken, Task> callback = (code, cancellation) =>
            {
                Console.WriteLine(code.Message);
                return Task.CompletedTask;
            };

            // Crear la credencial de código de dispositivo para la autenticación
            var deviceCodeCredential = new DeviceCodeCredential(
                callback, _tenantId, _clientId, options);

            // Crear una instancia de Microsoft Graph client usando el DeviceCodeCredential
            var graphClient = new GraphServiceClient(deviceCodeCredential, Scopes);

            // Obtener información del usuario autenticado
            var user = await graphClient.Me.GetAsync();

            // Mostrar los detalles del usuario autenticado
            Console.WriteLine($"Usuario autenticado: {user.DisplayName}");
            Console.WriteLine($"Correo electrónico: {user.Mail}");

            // Obtener y mostrar los primeros 5 mensajes del buzón de correo
            List<Microsoft.Graph.Models.Message>? messages = user.Messages?.ToList();

            Console.WriteLine("\nPrimeros 5 mensajes en el correo:");
            foreach (var message in messages)
            {
                Console.WriteLine($"Asunto: {message.Subject}");
                Console.WriteLine($"De: {message.Sender.EmailAddress.Address}");
            }
        }
    }
```

### 3.2. Explicación del Código

#### **Microsoft Graph Client**
- **`GraphServiceClient`**: Crea una instancia de Microsoft Graph Client, que utiliza la credencial de autenticación obtenida a través de **`DeviceCodeCredential`**.
- **Scopes**: Solicita permisos para leer la información del usuario (`User.Read`) y los correos electrónicos (`Mail.Read`).



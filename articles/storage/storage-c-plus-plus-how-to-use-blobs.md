---
title: Uso de Blob Storage (almacenamiento de objetos) en C++ | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: .net
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 53844120-1c48-4e2f-8f77-5359ed0147a4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 1f87e40edc8b6ad8567f2409e6df435ed66f2bbc
ms.openlocfilehash: 8571011cac1182a5bfdfe722c194fcd681712a02
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-blob-storage-from-c"></a>Cómo usar el almacenamiento de blobs de C++
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C++ y usan la [biblioteca de cliente de almacenamiento de Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar**, y **eliminar** blobs.  

> [!NOTE]
> Esta guía se destina a la biblioteca de cliente de almacenamiento de Azure para C++, versión 1.0.0 y posteriores. La versión recomendada es la biblioteca de cliente de almacenamiento 2.2.0, que se encuentra disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Creación de una aplicación de C++
En esta guía, usará las características de almacenamiento que se pueden ejecutar en una aplicación C++.  

Para ello, deberá instalar la biblioteca de cliente de almacenamiento de Azure para C++ y crear una cuenta de almacenamiento de Azure en su suscripción de Azure.   

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

* **Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
* **Windows:** en Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configuración de su aplicación para obtener acceso al almacenamiento de blobs
Agregue las siguientes instrucciones include en la parte superior del archivo C++ en el que desea usar las API de almacenamiento de Azure para obtener acceso a los blobs:  

```cpp
include <was/storage_account.h>
include <was/blob.h>
```

## <a name="setup-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure
Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, usando el nombre de su cuenta de almacenamiento y la clave de acceso de almacenamiento de la cuenta de almacenamiento que se muestra en [Azure Portal](https://portal.azure.com) para los valores *AccountName* y *AccountKey*. Para obtener información sobre las cuentas de almacenamiento y las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md). En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para probar la aplicación en el equipo local de Windows, puede usar Microsoft Azure [Storage Emulator](storage-use-emulator.md) que se instala con [Azure SDK](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una utilidad que simula los servicios Blob, Cola y Tabla disponibles en Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para mantener la cadena de conexión en el emulador de almacenamiento local:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar Azure Storage Emulator, seleccione el botón **Inicio** o pulse la tecla **Windows**. Comience a escribir **Azure Storage Emulator** y seleccione **Emulador de Microsoft Azure Storage** en la lista de aplicaciones.  

En los ejemplos siguientes se supone que usó uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.  

## <a name="retrieve-your-connection-string"></a>Recuperación de la cadena de conexión
Puede usar la clase **cloud_storage_account** para representar la información de la cuenta de almacenamiento. Para recuperar la información de la cuenta de almacenamiento a partir de la cadena de conexión de almacenamiento, puede usar el método **parse** .  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

A continuación, obtenga una referencia a una clase **cloud_blob_client**, ya que le permite recuperar objetos que representan contenedores y blobs almacenados en el servicio Blob Storage. El código siguiente crea un objeto **cloud_blob_client** usando el objeto de cuenta de almacenamiento recuperado anteriormente:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Cómo crear un contenedor
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si todavía no existe:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos (blobs) del contenedor a disposición de todo el mundo, puede convertir el contenedor en público usando el código siguiente:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Cómo cargar un blob en un contenedor
El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.  

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier flujo de datos en ella llamando al método **upload_from_stream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Como alternativa, puede usar el método **upload_from_file** para cargar un archivo en un blob en bloques.

## <a name="how-to-list-the-blobs-in-a-container"></a>Cómo enumerar los blobs en un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede usar el método **list_blobs** del contenedor para recuperar los blobs y los directorios que contiene. Para obtener acceso al conjunto completo de propiedades y métodos de un elemento **list_blob_item** devuelto, debe realizar una llamada al método **list_blob_item.as_blob** para obtener un objeto **cloud_blob** o al método **list_blob.as_directory** para obtener un objeto cloud_blob_directory. El código siguiente muestra cómo recuperar y consultar el URI de cada elemento del contenedor **my-sample-container** :

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Para obtener más información sobre cómo enumerar las operaciones, consulte [Enumeración de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Cómo descargar blobs
Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **download_to_stream**. En el siguiente ejemplo, se usa el método **download_to_stream** para transferir el contenido del blob a un objeto de flujo que, a continuación, puede guardar en un archivo local.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Como alternativa, puede usar el método **download_to_file** para descargar el contenido de un blob en un archivo.
También puede usar el método **download_text** para descargar el contenido de un blob en forma de cadena de texto.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Cómo eliminar blobs
Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **delete_blob** en ella.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información sobre Almacenamiento de Azure.  

* [Cómo usar el almacenamiento de colas de C++](storage-c-plus-plus-how-to-use-queues.md)
* [Cómo usar el almacenamiento de tablas de C++](storage-c-plus-plus-how-to-use-tables.md)
* [Enumeración de los recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
* [Referencia de la biblioteca de clientes de almacenamiento para C++](http://azure.github.io/azure-storage-cpp)
* [Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)



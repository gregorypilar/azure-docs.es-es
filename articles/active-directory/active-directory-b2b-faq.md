---
title: "Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory

Las preguntas frecuentes se actualizan periódicamente para reflejar nuevos intereses.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>¿Esta funcionalidad está disponible en el Portal de Azure clásico?
Las nuevas funcionalidades de la colaboración B2B de Azure AD solo están disponibles a través de [Azure Portal](https://portal.azure.com) y el nuevo panel de acceso. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>¿Es posible personalizar nuestra página de inicio de sesión, para que sea más intuitiva para los usuarios invitados de colaboración de B2B?
Por supuesto. Hay una [entrada de blog que explica la característica](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) y se documenta en el artículo [Incorporación de personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>¿Pueden acceder los usuarios de colaboración B2B a SharePoint Online y OneDrive?
Sí. Sin embargo, la capacidad de buscar usuarios invitados existentes en el selector de personas de SharePoint Online está desactivada de manera predeterminada para así coincidir con el comportamiento heredado. Para habilitarla, use la configuración "ShowPeoplePickerSuggestionsForGuestUsers" en el nivel de inquilino y colección de sitios. Para hacerlo, use los cmdlets Set-SPOTenant y Set-SPOSite, que permiten que los miembros busquen a todos los usuarios invitados existentes en el directorio. Los cambios en el ámbito del inquilino no afectan los sitios de SharePoint Online que ya se aprovisionaron.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>¿Se sigue admitiendo el mecanismo de carga CSV?
Sí. Consulte el [ejemplo de PowerShell](active-directory-b2b-code-samples.md) que hemos incluido.

### <a name="how-can-i-customize-my-invitation-emails"></a>¿Cómo puedo personalizar mis correos electrónicos de invitación?
Puede personalizar prácticamente cualquier elemento del proceso invitador con las [API de invitación de B2B](active-directory-b2b-api.md).

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>¿Puede dejar el usuario externo invitado la organización a la que se le invitó?
Esto no está disponible actualmente.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>¿Ahora que la autenticación multifactor (MFA) está disponible para los usuarios invitados, también pueden restablecer su método MFA?
Sí, de la misma manera que los usuarios normales.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>¿Qué organización es responsable de las licencias de MFA?
La organización invitadora es la que interviene y realiza la MFA. Por lo tanto, la organización invitadora es responsable de asegurarse de que disponen de suficientes licencias para sus usuarios B2B que realizan la MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>¿Qué ocurre si la organización de mi asociado ya tiene configurado la MFA? ¿Podemos confiar en su MFA y no usar la nuestra?
Se admitirá en próximas versiones. Cuando se publique, podrá seleccionar asociados concretos para excluirlos de la MFA de su organización invitadora.

### <a name="how-can-i-achieve-delayed-invitations"></a>¿Cómo puedo conseguir invitaciones diferidas?
Algunas organizaciones deseen agregar usuarios de colaboración B2B, aprovisionarlos a las aplicaciones que requieren aprovisionamiento y, luego, enviar las invitaciones. Si es el caso, puede usar la API de invitación de colaboración B2B para personalizar el flujo de trabajo de incorporación.

### <a name="can-i-make-my-guest-users-limited-admins"></a>¿Puedo convertir a mis usuarios invitados en administradores limitados?
Totalmente. Si es lo que su organización necesita, puede averiguar cómo hacerlo en el artículo sobre cómo [agregar usuarios invitados a un rol](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>¿Admite la colaboración B2B de Azure AD que los usuarios B2B puedan acceder a Azure Portal?
Los usuarios de colaboración B2B no deberían necesitar acceso a Azure Portal, a menos que se les asigne un rol de administrador limitado o global. En este caso, pueden acceder al portal. Si un usuario invitado que no tiene estos roles accede al portal, es posible que pueda acceder a determinadas partes de la experiencia porque el rol de usuario invitado tiene determinados permisos en el directorio, como se describe en las secciones anteriores.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>¿Puedo bloquearles el acceso a Azure Portal a los usuarios invitados?
Sí. Pero tenga cuidado al configurar esta directiva para evitar que se bloquee accidentalmente el acceso a los administradores y miembros.
Puede bloquearles el acceso a [Azure Portal](https://portal.azure.com) a los usuarios invitados a través de la directiva de acceso condicional de Service Management API de Windows Azure siguiendo los tres pasos de a continuación.
1. Modifique el grupo **Todos los usuarios** para que solo pueda contener miembros. ![captura de pantalla modificar el grupo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Cree un grupo dinámico que contenga usuarios invitados. ![captura de pantalla de crear grupo](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure una directiva de acceso condicional para evitar que los usuarios invitados accedan al portal, tal y como se muestra en el siguiente vídeo.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>¿Cuál es la escala de tiempo por la cual la colaboración B2B de Azure AD empezará a admitir cuentas de correo electrónico de consumidores y MFA?
Ahora, se admiten las cuentas de correo electrónico de consumidores y MFA.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>¿Hay un plan para admitir el restablecimiento de contraseñas para los usuarios de colaboración B2B de Azure AD?
Sí. Estos son los detalles sobre el restablecimiento de contraseña de autoservicio (SSPR) para un usuario de B2B que está invitado a un inquilino de recursos de su arrendatario de identidad:
 
* SSPR únicamente se realiza en el arrendatario de identidad del usuario de B2B.
* Si el arrendatario de identidad es una cuenta de Microsoft: utiliza el mecanismo SSPR de la cuenta de Microsoft.
* Si el arrendatario de identidad es un arrendatario just-in-time o viral, se enviará un correo electrónico de restablecimiento de contraseña.
* En otros casos, se seguirá el proceso estándar de SSPR para usuarios de B2B, similar al proceso de SSPR de miembros para usuarios de B2B en el contexto del arrendatario de recursos.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>¿Está también habilitada para los usuarios de un inquilino viral?
Actualmente, no.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>¿Microsoft CRM proporciona soporte técnico en línea para la colaboración B2B de Azure AD?
El proceso para su compatibilidad está en curso.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>¿Cuál es la duración de una contraseña inicial para un usuario de colaboración B2B recién creado?
Azure AD tiene un conjunto fijo de requisitos de bloqueo de cuentas, seguridad de la contraseña y caracteres que se aplican igualmente a todas las cuentas de usuario en la nube de Azure AD. Las cuentas de usuario en la nube son las cuentas que no se federan con otro proveedor de identidades como las cuentas Microsoft, Facebook, ADFS o incluso otro inquilino en la nube (en el caso de la colaboración B2B). En el caso de las cuentas federadas, la directiva de contraseñas depende de la directiva del espacio local y la configuración de cuenta Microsoft del usuario.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Las aplicaciones desean diferenciar su experiencia entre un usuario inquilino y un usuario invitado. ¿Hay instrucciones estándar para esto? ¿La presencia de la notificación del proveedor de identidades es el modelo adecuado para esto?
 
Un usuario invitado puede usar cualquier proveedor de identidades para autenticarse, tal como se analiza en [Propiedades de un usuario de colaboración B2B](active-directory-b2b-user-properties.md). Por lo tanto, la propiedad adecuada para determinar esto es UserType. La notificación UserType no se incluye actualmente en el token. Las aplicaciones deben usar API Graph para consultar el usuario en el directorio y obtener su UserType.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>¿Dónde puede encontrar una comunidad de colaboración B2B para compartir soluciones y enviar ideas?

Escuchamos constantemente sus comentarios sobre las formas de mejorar la colaboración B2B. Le invitamos a participar en el debate, compartir los escenarios de usuario, los procedimientos recomendados y lo que le gusta de la colaboración B2B de Azure AD en la [comunidad técnica de Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
También a enviar sus ideas y vote por características futuras en el sitio web de [ideas de colaboración B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>¿Hay una manera de invitar al usuario de forma que automáticamente se canjee la invitación y el usuario esté "preparado", o bien el usuario siempre tendrá que desplazarse a la dirección URL de canje?
Las invitaciones enviadas por un usuario de la organización invitadora que también es miembro de la organización invitada (la organización del usuario de B2B) no requieren canje por el usuario de B2B.

Se recomienda que lo consiga mediante la invitación de un usuario de la organización invitada a la organización invitadora. [Agregue este usuario al rol de del autor de la invitación del invitado en la organización de recursos](active-directory-b2b-add-guest-to-role.md). Este usuario puede invitar a otros usuarios de la organización invitada a través de la interfaz de usuario de inicio de sesión, los scripts de PowerShell o las API sin solicitar al usuario de B2B de esa organización que canjee su invitación.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>¿Cómo funciona la colaboración B2B cuando el asociado invitado utiliza la federación para agregar su propia autenticación local?
Si el asociado tiene un inquilino de Azure AD que está federado en la infraestructura de autenticación local, el inicio de sesión único (SSO) local se consigue automáticamente. Si el asociado no tiene ningún inquilino de Azure AD, se crea una cuenta de Azure AD para que acceda el usuario. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>No creo que B2B de Azure AD deba aceptar direcciones de correo electrónico de gmail.com y outlook.com. B2C era lo que se usó.
Estamos quitando las diferencias entre B2B y B2C en relación con las identidades que se admiten. La identidad utilizada no es un buen punto para decidir entre usar B2B o B2C. Consulte este artículo como ayuda para decidir cuál utilizar: [Comparación de la colaboración B2B y B2C de Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>¿Qué aplicaciones y los servicios admiten los usuarios invitados de Azure B2B?
Todas las aplicaciones integradas de Azure AD. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>¿Es posible aplicar MFA a usuarios invitados de B2B si los asociados no tienen ningún MFA habilitado?
Sí. Puede encontrar información en [Acceso condicional para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md).

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>En SharePoint puede definir una lista de "permitidos" o "denegados" para usuarios externos. ¿Hay algún plan para ampliarlo a Azure o a todas las aplicaciones de Office 365?
Sí. La colaboración B2B en Azure AD admite característica de lista de permitidos o denegados. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>¿Qué licencias se necesitan para B2B de Azure AD?
Consulte [Guía de concesión de licencias de colaboración B2B de Azure Active Directory](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Personalización y API de colaboración B2B de Active Directory Azure](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)


---
title: "Open Auth"
date: 2024-06-20T11:36:46+02:00
weight: 2
---

## Introduction

[Open Auth](https://github.com/litarvan/openauth) est une librairie Java développée par [Litarvan](https://github.com/litarvan) qui offre une méthode rapide pour s'authentifier via les services officiels pour un client Minecraft.
Dans cet article, nous parlerons exclusivement de l'authentification (cité sous le nom "d'auth" à partir de maintenant) par un compte Microsoft. En effet, désormais Mojang supporte uniquement cette méthode.

## Importer la librairie

Cette librairie est hébergée sur le maven de Litarvan, vous pouvez donc l'importer dans votre projet comme ceci :

Avec Gradle :

```groovy {filename=build.gradle}
repositories {
    mavenCentral()
    maven {
        url = 'https://jitpack.io'
        name = 'JitPack'
    }
}

dependencies {
    implementation 'com.github.litarvan:openauth:518589d222'
}

```

Avec Maven :

```xml {filename=pom.xml}
<repositories>
    <repository>
        <id>JitPack</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
<dependencies>
    <dependency>
        <groupId>com.github.litarvan</groupId>
        <artifactId>openauth</artifactId>
        <version>518589d222</version>
    </dependency>
</dependencies>
```

{{< callout emoji="✅" type="info" >}}
À l'heure où est écrit ce tutoriel, la dernière version disponible est la
**1.1.5** (qui est le commit `518589d222`, puisque cette version n'a pas été publié). Allez sur la page des
[releases](https://github.com/Litarvan/OpenAuth/releases) pour voir la
dernière version disponible.
{{< /callout >}}

## Authentification

Plusieurs méthodes d'auth sont disponibles en fonction des besoins de l'application que vous développez.

{{< callout emoji="⚠️" type="warning" >}}
Il faut choisir **une seule** méthode d'auth parmi les suivantes.
{{< /callout >}}

Pour toutes les méthodes d'auth suivantes, il faut déclarer un objet de type `MicrosoftAuthenticator` comme ceci :

```java
MicrosoftAuthenticator authenticator = new MicrosoftAuthenticator();
```

### Auth par "Credentials":

Cette méthode permet de se connecter avec l'email et le mot de passe du compte Microsoft, elle peut être utile dans le cas ou l'application intègre ses propres champs de texte pour la connexion.
Nonobstant, cette méthode est déconseillée car elle n'est pas sécurisée, et peut être utilisée pour récupérer des informations sensibles par l'application (puisqu'elle traîte directement les informations de connexion). De plus, cette méthode ne supporte pas la double authentification "A2F".
Voici comment l'utiliser :

```java
// ...
MicrosoftAuthResult result = authenticator.loginWithCredentials("email", "password");
```

### Auth par "WebView":

Cette méthode permet de se connecter avec une interface de connexion Microsoft, comme ceci :

![Image de la webview Microsoft](https://sharepointmaven.com/wp-content/uploads/2022/09/brandloginwindowmicrosoft3651.jpg)

Voici comment l'utiliser :

```java
...
MicrosoftAuthResult result = authenticator.loginWithWebView();
```

{{< callout emoji="✅" type="info" >}}
Attention, cette méthode n'est pas une méthode asynchrone, c'est a dire que
votre programme ne tournera plus en arrière plan lorsque cette fenêtre sera
ouverte si elle n'est pas ouverte dans un autre thread. Cela peut causer des
problèmes, par exemple : votre interface graphique pourra être gelée et vous
aurez un beau message de votre OS vous informant que le programme ne répond
plus.
{{< /callout >}}

Si vous voulez utiliser la méthode asynchrone, utilisez simplement `loginWithAsyncWebView` au lieu de `loginWithWebView`;

### Auth par "RefreshToken:

Cette méthode d'auth permet de se connecter avec un _refresh_token_, elle est utilisée pour éviter de se reconnecter à chaque lancement.
Le _refresh_token_ est récupérable avec une authentification basique, sauvegardez le dans un fichier pour le récupérer au prochain lancement.
Voici comment l'utiliser :

```java
// ...
MicrosoftAuthResult result = authenticator.loginWithRefreshToken("refresh token");
```

### Auth par "DeviceId"

_Cette auth est en cours d'écriture, elle permettrait de ne pas utiliser la webView et d'ouvrir une page internet pour taper un code, comme il est fait dans le launcher de multiMc_

## Résultat

Pour toutes ces méthodes d'auth, nous obtenons un objet result de type `MicrosoftAuthResult`. Cet objet nous permet de récupérer plusieurs informations sur le compte que nous venons de connecter :

- le profil avec la méthode `result.getProfile()`
- l'accesToken avec la méthode `result.getAccessToken()`
- le refreshToken avec la méthode `result.getRefreshToken()`
- l'xuid avec la méthode `result.getXuid()`
- le clientId avec la méthode `result.getClientId()`

Les deux derniers ids sont nécessaires notamment pour les authentifications à partir de la 1.18.

### Le profil

La méthode `result.getProfile()` retourne uen objet de type `MinecraftProfile`, qui peut être utilisé avec les méthodes suivantes :

- `getId()` permets de récupérer l'UUID du compte minecraft
- `getName()` permets de récupérer le nom du compte du joueur
- `getSkins()` permets de récupérer la liste des skins du joueur sous forme de MinecraftSkin[] (je ne détaillerais pas ici l'utilisation de cette classe que vous pouvez trouver [ici](https://github.com/Litarvan/OpenAuth/blob/518589d22210bf0763d9ae19d546ddf1dba3fb8c/src/main/java/fr/litarvan/openauth/microsoft/model/response/MinecraftProfile.java#L60))

### Les tokens

- L'_acces token_ est utilisé pour lancer le jeu, par exemple avec la librairie [OpenLauncherLib](https://github.com/FlowArg/OpenLauncherLib)
- Le _refresh token_, dont nous avons déjà parlé avant, permet quant à lui de se reconnecter directement, sans avoir à rentrer de mot de passe, il est donc pratique de le sauvegarder dans un fichier après la connexion.

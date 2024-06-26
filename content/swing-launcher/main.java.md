---
title: "Main.java"
linkTitle: "Getting started"
date: 2024-06-20T11:36:46+02:00
---

# Main.java

Cette classe étends la classe Jframe, donc nous pouvons directement utiliser la fonction Main() pour mettre en place la fenêtre.

Nous mettons en place : - Le titre de la fenêtre - La taille - L'exinction du process java lorsqu'on quitte la fenêtre (bouton rouge) - Le contenu de la fenêtre (JPanel) - L'icon du launcher - Si la fenêtre est redimensionnable.

{{< callout type="error" emoji="⚠️" >}}
L'interface proposée est à titre d'exemple, merci de créer la votre !
{{< /callout >}}

## isConnected?

```java
MicrosoftAuthenticator microsoftAuthenticator = new MicrosoftAuthenticator();
final String refresh_token = getSaver().get("refresh_token");
MicrosoftAuthResult result;

if (refresh_token != null) {
    try {
        result = microsoftAuthenticator.loginWithRefreshToken(refresh_token);
    } catch (MicrosoftAuthenticationException ex) {
        throw new RuntimeException(ex);
    }
    Launcher.authInfos = new AuthInfos(result.getProfile().getName(), result.getAccessToken(), result.getProfile().getId());
    System.out.printf("Logged in with '%s'%n", result.getProfile().getName());
    setContentPane(new PHome());
    repaint();
    revalidate();
}
```

Ce bout de code vérifie si un compte microsoft (refresh_token, **pas les informations d'auth**) est sauvegardé dans le fichier du saver. Si c'est le cas, Le launcher se lance correctement, sinon une WebView de connexion s'affichera.

## Un peu d'amusement

### Déplacement de fenêtre

J'ai ajouté une fonction qui permet aux utilisateurs de bouger la fenêtre avec leur souris depuis l'intérieur de la fenêtre et non depuis la barre des tâches.

#### Theme?

J'ai également ajouté un thème swing (intellij theme).

{{< callout type="information" emoji="🛠" >}}
**Vous n'êtes aboslument pas obligé de garder ces ajouts.**
{{< /callout >}}

## Afficher la fenêtre

```java
Main main = new Main();
```

Cette ligne permet seulement d'afficher la fenêtre.

---
title: "Main.java"
date: 2024-06-20T11:36:46+02:00
---

{{< callout type="information" emoji="❤" >}}
noFramework est maintenant utilisé pour le lancement du jeu !
{{< /callout >}}

Cette classe permet de lancer un client minecraft en utilisant FlowUpdater. Vous pouvez trouver plus d'informations à ce propos ici: [FlowUpdater](../../flow-updater)

## Launch function

```java
public static void launch() {
  try {
    NoFramework noFramework = new NoFramework(
      gameDir,
      authInfos,
      GameFolder.FLOW_UPDATER
    );
    noFramework.getAdditionalVmArgs().add("-Xms1G");
    noFramework.getAdditionalVmArgs().add("-Xmx" + getSaver().get("ram") + "G");

    Process p = noFramework.launch("1.8.8", "", NoFramework.ModLoader.VANILLA);
  } catch (Exception e) {
    logger.printStackTrace(e);
  }
}
```

### Vm arguments

Avec `"noFramework.getAdditionalVmArgs().add("eheh i'm a Vm argument");`, vous pouvez ajouter un argument à la jvm, mais vous n'avez pas enormément de possibilité d'utilisation pour un client minecraft. Généralement, vous allez l'utiliser pour argument concernant l'allocation en mémoire.

### Launch Process

Le lancement est un process, donc vous pouvez obtenir certaines informations, comme par exemple de savoir s'il est toujours fonctionnel. [Plus d'informations](https://docs.oracle.com/javase/8/docs/api/java/lang/Process.html)

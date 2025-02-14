---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
editor: true
title: Formation React Native et Expo
#transition: slide-left
download: "https://react-native.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://d33wubrfki0l68.cloudfront.net/554c3b0e09cf167f0281fda839a5433f2040b349/ecfc9/img/header_logo.svg"
  eventUrl: "https://reactnative.dev/"
  twitter: ""
  twitterUrl: "https://twitter.com/reactnative"
---

# React Native et Expo

Une formation complète sur le développement d'applications mobiles avec React Native et Expo.

<div class="pt-12">
  <span @click="next" class="px-2 p-1 rounded cursor-pointer hover:bg-white hover:bg-opacity-10">
    Appuyez sur espace pour la page suivante <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: two-cols
routeAlias: 'sommaire'
---

<a name="SOMMAIRE" id="sommaire"></a>

# SOMMAIRE 📜

Voici le sommaire de cette formation sur React Native et Expo:

<small>

<div class="flex flex-col gap-2">
<Link to="introduction-react-native">🚀 Introduction à React Native et Expo</Link>
<Link to="configuration-environnement">💻 Configuration de l'environnement de développement</Link>
<Link to="ejection-react-native">🔧 Éjection de React Native</Link>
<Link to="bases-react-native">🔧 Bases de React Native</Link>
<Link to="composants-natifs-styling">📱 Composants natifs et styling</Link>
<Link to="gestion-etat-navigation">📊 Gestion de l'état et navigation</Link>
</div>

</small>

::right::

<small>

<div class="flex flex-col gap-2">
<Link to="integration-api-donnees">🌐 Intégration d'API et gestion des données</Link>
<Link to="camera-medias">📸 Utilisation de la caméra et des médias</Link>
<Link to="animations-gestes">🎨 Animations et gestes</Link>
<Link to="publication-application">📦 Publication de l'application</Link>
<Link to="bonnes-pratiques-optimisation">🔍 Bonnes pratiques et optimisation</Link>
<Link to="code-source">🧑‍💻 Code source du projet</Link>
</div>

</small>

---
src: ./pages/introduction.md
---

---
src: ./pages/configuration.md
---

---
src: ./pages/ejection.md
---

---
src: ./pages/bases_react_native.md
---

---
src: ./pages/composants_natifs.md
---

---
src: ./pages/gestion_etat.md
---

---
src: ./pages/integration_api.md
---

---
src: ./pages/camera_medias.md
---

---
src: ./pages/animations_gestes.md
---

---
src: ./pages/publication.md
---

---
src: ./pages/bonnes_pratiques.md
---

---
routeAlias: 'code-source'
---

# Code source du projet d'exercice : 

*Ci dessous le lien du projet :*

[Lien du projet - Github](https://github.com/JSurquin/expo-tinder-like-sample)
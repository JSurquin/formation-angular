---
theme: ./
colorSchema: "auto"
layout: intro
highlighter: shiki
editor: true
title: Formation Angular 18/19
#lineNumbers: true
record: "dev"
lang: "en"
#transition: slide-left
download: "https://angular.andromed.fr/slides.pdf"
themeConfig:
  logoHeader: "/avatar.png"
  eventLogo: "https://angular.io/assets/images/logos/angular/angular.svg"
  eventUrl: "https://angular.io/"
  twitter: ""
  twitterUrl: "https://twitter.com/angular"
---

# Angular 18/19

Une formation complète sur le développement d'applications web modernes avec Angular.

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

Voici le sommaire de cette formation sur Angular 18/19:

<small>

<div class="flex flex-col gap-2">
<Link to="introduction-angular">🚀 Introduction à Angular</Link>
<Link to="typescript-essentials">💡 TypeScript Essentiels</Link>
<Link to="configuration-environnement">💻 Configuration de l'environnement</Link>
<Link to="angular-basics">🎯 Les Bases d'Angular</Link>
<Link to="composants-angular">🔧 Composants Angular</Link>
<Link to="tailwind-setup">🎨 Tailwind v4 dans Angular</Link>
<Link to="template-syntax">📝 Syntaxe des templates</Link>
<Link to="routing-navigation">🗺️ Routing et Navigation</Link>
<Link to="component-lifecycle">🔄 Cycle de vie des composants</Link>
</div>

</small>

::right::

<small>

<div class="flex flex-col gap-2">
<Link to="forms-validation">📝 Formulaires et Validation</Link>
<Link to="services-dependency-injection">📊 Services</Link>
<Link to="dependency-injection">💉 Injection de dépendances</Link>
<Link to="signals">⚡ Signals (Nouveauté Angular 18)</Link>
<Link to="rxjs-observables">🔄 RxJS et Observables</Link>
<Link to="http-client">🌐 HTTP Client et API REST</Link>
<Link to="directives-pipes">📱 Directives et Pipes</Link>
<Link to="performance">🚀 Performance et Optimisation</Link>
<Link to="testing">🧪 Tests unitaires et E2E</Link>
<Link to="deployment">📦 Déploiement</Link>
<Link to="best-practices">🔍 Bonnes pratiques</Link>
<a href="https://github.com/JSurquin/angular-19-mini-blog" target="_blank">🧑‍💻 Code source du projet</a>
</div>

</small>

---
src: ./pages/introduction.md
---

---
src: ./pages/typescript_essentials.md
---

---
src: ./pages/configuration.md
---

---
src: ./pages/angular_basics.md
---

---
src: ./pages/composants.md
---

---
src: ./pages/tailwind.md
---

---
src: ./pages/template_syntax.md
---

---
src: ./pages/routing.md
---

---
src: ./pages/component_lifecycle.md
---

---
src: ./pages/forms.md
---

---
src: ./pages/services.md
---

---
src: ./pages/dependency_injection.md
---

---
src: ./pages/signals.md
---

---
src: ./pages/rxjs.md
---

---
src: ./pages/http.md
---

---
src: ./pages/directives.md
---

---
src: ./pages/performance.md
---

---
src: ./pages/testing.md
---

---
src: ./pages/deployment.md
---

---
src: ./pages/best_practices.md
---

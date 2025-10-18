# TP0 - Introduction à Jakarta EE

**Étudiant : **BENATIK Oussama  
**Numéro : **6  
**Date :** Octobre 2025  
**Module :** Agents conversationnels en Java avec LangChain4j [EMSI - UCA - I2A]

---

## 📋 Table des matières

- [Description du projet](#description-du-projet)
- [Technologies utilisées](#technologies-utilisées)
- [Architecture du projet](#architecture-du-projet)
- [Installation et déploiement](#installation-et-déploiement)
- [Fonctionnalités](#fonctionnalités)
- [Traitement personnalisé](#traitement-personnalisé)
- [Explications techniques](#explications-techniques)
- [Captures d'écran](#captures-décran)
- [Difficultés rencontrées](#difficultés-rencontrées)
- [Améliorations possibles](#améliorations-possibles)

---

## 📖 Description du projet

Application web Jakarta EE simulant une interface de chat. L'utilisateur peut choisir un rôle système, poser des questions, et recevoir des réponses traitées par le serveur. Cette application sert de base pour les TPs suivants où un vrai LLM sera intégré.

**Objectifs pédagogiques :**
- Maîtriser Maven pour la gestion de projet
- Comprendre Git et GitHub pour le versionnement
- Utiliser CDI (Context and Dependency Injection)
- Créer des interfaces avec JSF (Jakarta Server Faces)
- Configurer et déployer sur Payara Server

---

## 🛠️ Technologies utilisées

| Technologie | Version | Usage |
|-------------|---------|-------|
| **Java** | 17 | Langage de programmation |
| **Jakarta EE** | 10 | Framework d'entreprise |
| **JSF (Jakarta Faces)** | 4.0 | Interface utilisateur web |
| **CDI** | 4.0 | Injection de dépendances |
| **PrimeFaces** | 15.0.7 | Composants UI avancés |
| **Maven** | 3.3.2 | Gestion de projet et dépendances |
| **Payara Server** | 6.2025.9 | Serveur d'application |
| **Git/GitHub** | - | Versionnement et collaboration |

---

## 📁 Architecture du projet
```
tp0_jakartaee/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── ma/emsi/benatik/tp0_jakartaee/
│   │   │       ├── jsf/
│   │   │       │   ├── Bb.java                    # Backing bean principal
│   │   │       │   └── CharsetFilter.java         # Filtre UTF-8
│   │   ├── resources/
│   │   │   └── META-INF/
│   │   │       └── persistence.xml                # Configuration JPA
│   │   └── webapp/
│   │       ├── WEB-INF/
│   │       │   ├── beans.xml                      # Configuration CDI
│   │       │   └── web.xml                        # Configuration web
│   │       ├── resources/
│   │       │   ├── css/
│   │       │   │   └── mycsslayout.css            # Styles CSS
│   │       │   └── js/
│   │       │       └── script.js                  # Scripts JavaScript
│   │       └── index.xhtml                        # Page JSF principale
│   └── test/
├── pom.xml                                         # Configuration Maven
└── README.md                                       # Ce fichier
```

---

## 🚀 Installation et déploiement

### **Prérequis**

- JDK 21
- Maven 3.6+
- Payara Server 6.2025.9
- Git

### **Étapes d'installation**

1. **Cloner le repository**
```bash
   git clone https://github.com/Ou55am1/tp0_benatik.git
   cd tp0_jakartaee
```

2. **Compiler le projet**
```bash
   mvn clean package
```

3. **Déployer sur Payara**
```bash
   cd [PAYARA_HOME]/glassfish/bin
   asadmin start-domain domain1
   asadmin --port 4849 deploy --force=true "C:\Users\HP ELITEBOOK 830 G5\Documents\NetBeansProjects\tp0_jakartaee\target\tp0_jakartaee.war"
```

4. **Accéder à l'application**
```
   http://localhost:9090/tp0_jakartaee/
```

---

## ✨ Fonctionnalités

### **Fonctionnalités principales**

- ✅ **Sélection du rôle système** via liste déroulante
- ✅ **Saisie de questions** dans une zone de texte
- ✅ **Affichage des réponses** traitées par le serveur
- ✅ **Historique de conversation** en temps réel
- ✅ **Validation** : message d'erreur si question vide
- ✅ **Blocage du rôle** après le premier message
- ✅ **Bouton "Nouveau chat"** pour réinitialiser
- ✅ **Encodage UTF-8** pour les accents
- ✅ **Boutons de copie rapide** pour chaque zone de texte

### **Rôles système disponibles**

1. **Assistant** : Aide générale
2. **Traducteur Anglais-Français** : Traduction bidirectionnelle
3. **Guide touristique** : Informations de voyage

---

## 🎨 Traitement personnalisé

### **Transformation Miroir**

**Description :**  
J'ai implémenté une fonction de "miroir magique" qui inverse la question caractère par caractère. L'utilisateur voit sa question originale et sa version inversée côte à côte.

**Code (extrait de `Bb.java`) :**
```java
// Créer l'inverse de la question
String inverse = new StringBuilder(question).reverse().toString();

// Construction de la réponse avec la transformation miroir
this.reponse += String.format(
    "MIROIR MAGIQUE \n\n" +
    "Original → %s\n" +
    "Inversé  ← %s\n\n" +
    "Astuce : Essayez avec des palindromes comme 'kayak' !",
    question, inverse
);
```

**Exemple d'utilisation :**

| Question | Réponse inversée |
|----------|------------------|
| `Bonjour` | `ruojnoB` |
| `Hello World` | `dlroW olleH` |
| `kayak` | `kayak` (palindrome !) |
| `radar` | `radar` (palindrome !) |

**Intérêt pédagogique :**
- Utilisation de `StringBuilder` et sa méthode `reverse()`
- Formatage de chaînes avec `String.format()`
- Découverte des palindromes

---

## 🔧 Explications techniques

### **1. Le backing bean (Bb.java)**

**Rôle :** Gère la logique métier de l'application.

**Annotations importantes :**
- `@Named` : Rend le bean accessible dans les pages JSF
- `@ViewScoped` : Le bean existe tant que l'utilisateur reste sur la page
- `@Inject` : Injection du contexte JSF

**Propriétés principales :**
```java
private String question;              // Question de l'utilisateur
private String reponse;               // Réponse du serveur
private StringBuilder conversation;   // Historique complet
private String roleSysteme;           // Rôle choisi
private boolean roleSystemeChangeable; // Bloquage du rôle
```

---

### **2. Validation et messages d'erreur**

**Question :** Comment le message d'erreur s'affiche-t-il quand la question est vide ?

**Réponse en 3 parties :**

#### **a) Détection dans le backing bean (`Bb.java`)**
```java
if (question == null || question.isBlank()) {
    FacesMessage message = new FacesMessage(
        FacesMessage.SEVERITY_ERROR,
        "Texte question vide",
        "Il manque le texte de la question"
    );
    facesContext.addMessage(null, message);
    return null;
}
```

- La condition détecte que la question est vide ou ne contient que des espaces
- Un objet `FacesMessage` de type `SEVERITY_ERROR` est créé
- Le message est ajouté au contexte JSF via `facesContext.addMessage()`

#### **b) Affichage dans la page JSF (`index.xhtml`)**
```xml
<h:messages globalOnly="true" styleClass="error-messages"/>
```

- Le composant `<h:messages>` interroge le contexte JSF
- Il affiche automatiquement tous les messages d'erreur présents
- L'attribut `globalOnly="true"` affiche les messages globaux

#### **c) Style CSS (optionnel)**
```css
.error-messages {
    color: red;
    font-weight: bold;
    margin: 10px 0;
}
```

- Les messages sont stylisés en rouge et en gras
- Améliore la visibilité pour l'utilisateur

---

### **3. Le filtre UTF-8 (CharsetFilter.java)**

**Problème :** Sans ce filtre, les caractères accentués (é, è, à, ç) s'affichent mal.

**Solution :**
```java
@WebFilter(filterName = "CharsetFilter", urlPatterns = {"/*"})
public class CharsetFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        request.setCharacterEncoding("UTF-8");
        chain.doFilter(request, response);
    }
}
```

**Fonctionnement :**
1. Le filtre intercepte **toutes** les requêtes (`/*`)
2. Force l'encodage UTF-8 **avant** le traitement par JSF
3. Continue la chaîne avec `chain.doFilter()`

---

### **4. Gestion des portées CDI**

**ViewScoped** a été choisi pour le backing bean car :

| Portée | Durée de vie | Cas d'usage |
|--------|--------------|-------------|
| `@RequestScoped` | Une requête HTTP | ❌ Trop court (perd les données) |
| `@ViewScoped` | Tant qu'on reste sur la page | ✅ **Parfait pour notre chat** |
| `@SessionScoped` | Toute la session | ❌ Trop long (occupe la mémoire) |

---

## 📸 Captures d'écran

### **Interface principale**

![Interface principale](<img width="1731" height="820" alt="Capture d'écran 2025-10-19 003344" src="https://github.com/user-attachments/assets/79f42564-edc6-4c48-90bb-6a15845ff340" />)
*L'utilisateur peut choisir un rôle et poser des questions*

### **Transformation miroir en action**

![Transformation miroir](<img width="1727" height="750" alt="image" src="https://github.com/user-attachments/assets/9ddb72d9-9876-46af-8ef0-6f9d7041d4de" />)
*Exemple avec le mot "Bonjour" inversé en "ruojnoB"*

### **Message d'erreur**

![Message d'erreur](<img width="1732" height="772" alt="image" src="https://github.com/user-attachments/assets/9440667f-9bf3-46cb-92a0-80d88ad1d37c" />)
*Validation : la question ne peut pas être vide*

---

## 🚧 Difficultés rencontrées

### **1. Configuration des ports Payara**

**Problème :** Payara utilisait le port 4849 au lieu de 4848 par défaut.

**Solution :** Ajouter `--port 4849` dans toutes les commandes `asadmin`.

---

### **2. Déploiement depuis NetBeans**

**Problème :** L'option "Deploy" n'apparaissait pas dans le menu contextuel.

**Solution :** 
- Vérifier `<packaging>war</packaging>` dans `pom.xml`
- Déployer manuellement via `asadmin deploy`

---

### **3. Encodage des caractères**

**Problème :** Les accents ne s'affichaient pas correctement.

**Solution :** Création du filtre `CharsetFilter` pour forcer UTF-8.

---

## 📄 Licence

Ce projet est réalisé dans un cadre pédagogique à l'EMSI.

---

## ✉️ Contact

**Étudiant :** BENATIK Oussama 
**Email :** Oussama.Benatik@emsi-edu.ma / benatik.oussama@gmail.com 
**GitHub :** [https://github.com/Ou55am1](https://github.com/Ou55am1)

---

**Date de remise :** Octobre 2025  
**Status :** ✅ Terminé

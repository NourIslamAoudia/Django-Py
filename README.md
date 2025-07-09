# 🚀 Guide Complet Django - De Zéro à Expert

## 📋 Table des Matières
1. [Introduction à Django](#introduction-à-django)
2. [Architecture du Projet](#architecture-du-projet)
3. [Configuration Initiale](#configuration-initiale)
4. [Modèles (Models)](#modèles-models)
5. [Vues (Views)](#vues-views)
6. [Templates & DTL](#templates--dtl)
7. [URLs et Routage](#urls-et-routage)
8. [Administration Django](#administration-django)
9. [Fichiers Statiques et Médias](#fichiers-statiques-et-médias)
10. [Base de Données](#base-de-données)
11. [Déploiement](#déploiement)

---

## 📖 Introduction à Django

**Django** est un framework web Python de haut niveau qui encourage un développement rapide et un design propre et pragmatique. Il suit le principe **"Don't Repeat Yourself" (DRY)** et utilise l'architecture **Model-View-Template (MVT)**.

### 🎯 Philosophie Django
- **Rapide** : Django a été conçu pour aider les développeurs à passer de la conception à la réalisation le plus rapidement possible
- **Sécurisé** : Django prend la sécurité au sérieux et aide les développeurs à éviter de nombreuses erreurs de sécurité courantes
- **Évolutif** : Certains des sites les plus fréquentés du Web utilisent la capacité de Django à évoluer rapidement et de manière flexible

---

## 🏗️ Architecture du Projet

### Structure MVT (Model-View-Template)
```
Django-Py/
├── 📁 project/                 # Configuration principale
│   ├── settings.py            # Configuration globale
│   ├── urls.py               # URLs principales
│   ├── wsgi.py               # Interface WSGI
│   └── asgi.py               # Interface ASGI
├── 📁 person/                # Application "person"
│   ├── models.py             # Modèles de données
│   ├── views.py              # Logique métier
│   ├── urls.py               # URLs de l'app
│   ├── admin.py              # Interface admin
│   └── migrations/           # Migrations DB
├── 📁 pages/                 # Application "pages"
├── 📁 templates/             # Templates HTML
├── 📁 static/               # Fichiers statiques
├── 📁 photos/               # Fichiers médias
└── manage.py                # Utilitaire Django
```

### 🔄 Flux de Traitement Django
1. **URL** → Django reçoit une requête et vérifie `urls.py`
2. **View** → La vue correspondante traite la requête
3. **Model** → Si nécessaire, interaction avec la base de données
4. **Template** → Génération du HTML avec le contexte
5. **Response** → Envoi de la réponse au client

---

## ⚙️ Configuration Initiale

### 1. Installation et Création du Projet
```bash
# Installation de Django
pip install django

# Création du projet
django-admin startproject project .

# Création d'applications
python manage.py startapp person
python manage.py startapp pages
```

### 2. Configuration `settings.py`
```python
# Applications installées
INSTALLED_APPS = [
    'person.apps.PersonConfig',    # Notre app person
    'pages.apps.PagesConfig',      # Notre app pages
    'django.contrib.admin',        # Interface d'administration
    'django.contrib.auth',         # Système d'authentification
    'django.contrib.contenttypes', # Types de contenu
    'django.contrib.sessions',     # Gestion des sessions
    'django.contrib.messages',     # Système de messages
    'django.contrib.staticfiles',  # Fichiers statiques
]

# Configuration de la base de données MySQL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django_db',
        'USER': 'root',
        'PASSWORD': 'islam',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}

# Configuration des templates
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, "templates")],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

## 📊 Modèles (Models)

Les modèles définissent la structure des données et gèrent l'interaction avec la base de données.

### 📝 Exemple : Modèle Person
```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    age = models.IntegerField()
    bio = models.TextField()
    image = models.ImageField(upload_to='photos/%Y/%m/%d')
    active = models.BooleanField(default=False)
    en_ligne = models.BooleanField(default=False)

    def __str__(self):
        return f"{self.first_name} {self.last_name}"
    
    class Meta:
        ordering = ['first_name']
        verbose_name_plural = 'People'
```

### 🛠️ Types de Champs Courants
- `CharField` : Texte court avec longueur limitée
- `TextField` : Texte long
- `IntegerField` : Nombres entiers
- `BooleanField` : Valeurs booléennes (True/False)
- `DateTimeField` : Date et heure
- `ImageField` : Upload d'images
- `ForeignKey` : Relation un-à-plusieurs
- `ManyToManyField` : Relation plusieurs-à-plusieurs

### 📋 Migrations
```bash
# Créer les migrations
python manage.py makemigrations

# Appliquer les migrations
python manage.py migrate

# Voir l'état des migrations
python manage.py showmigrations
```

---

## 👁️ Vues (Views)

Les vues contiennent la logique métier et traitent les requêtes HTTP.

### 🎯 Vues Basées sur des Fonctions
```python
from django.shortcuts import render
from .models import Person

def person(request):
    """Affiche une personne spécifique"""
    persons = Person.objects.filter(first_name="islam")
    if persons.exists():
        person = persons.first()
    return render(request, 'person/person.html', {'person': person})

def persons(request):
    """Affiche toutes les personnes"""
    all_persons = Person.objects.all()
    return render(request, 'person/persons.html', {'persons': all_persons})

def index1(request):
    """Page d'accueil"""
    context = {
        'name': 'Islam Aoudia Nour',
        'age': 25
    }
    return render(request, "pages/index.html", context)
```

### 🔍 ORM Django (Object-Relational Mapping)
```python
# Récupérer tous les objets
Person.objects.all()

# Filtrer les objets
Person.objects.filter(active=True)

# Récupérer un objet unique
Person.objects.get(id=1)

# Créer un nouvel objet
person = Person.objects.create(
    first_name="John",
    last_name="Doe",
    age=30
)

# Mettre à jour un objet
person.age = 31
person.save()

# Supprimer un objet
person.delete()
```

---

## 🎨 Templates & DTL (Django Template Language)

Le **Django Template Language (DTL)** est le moteur de templates intégré à Django qui permet de générer dynamiquement du contenu HTML.

### 🔧 Syntaxe de Base
DTL utilise une syntaxe claire avec :
- `{{ variable }}` : Affichage des variables
- `{% tag %}` : Instructions et balises de contrôle
- `{# commentaire #}` : Commentaires

### 📋 Variables et Contexte
```django
<!-- Affichage de variables -->
<h1>Bienvenue, {{ user.username }}!</h1>
<p>Votre email : {{ user.email }}</p>
<p>Âge : {{ person.age }} ans</p>
```

### 🔀 Structures de Contrôle

#### Conditions (`if/elif/else`)
```django
{% if user.is_superuser %}
    <p>🔥 Bonjour, Super Administrateur!</p>
{% elif user.is_staff %}
    <p>👨‍💼 Bienvenue, membre de l'équipe!</p>
{% elif user.is_authenticated %}
    <p>👋 Bonjour, utilisateur authentifié!</p>
{% else %}
    <p>🔐 Veuillez vous connecter pour accéder à ce contenu.</p>
{% endif %}
```

#### Boucles (`for`)
```django
<ul>
{% for person in persons %}
    <li>
        <strong>{{ person.first_name }} {{ person.last_name }}</strong>
        {% if person.active %}
            <span class="badge active">Actif</span>
        {% else %}
            <span class="badge inactive">Inactif</span>
        {% endif %}
    </li>
{% empty %}
    <li>Aucune personne trouvée.</li>
{% endfor %}
</ul>
```

### 🎛️ Filtres
Les filtres transforment les données avant affichage :
```django
{{ user.email|lower }}                    <!-- Convertit en minuscules -->
{{ person.bio|truncatewords:10 }}         <!-- Limite à 10 mots -->
{{ person.active|yesno:"Actif,Inactif" }} <!-- Convertit booléen en texte -->
{{ person.created_at|date:"d/m/Y" }}      <!-- Formate la date -->
{{ price|floatformat:2 }}                 <!-- 2 décimales -->
```

### 🏗️ Héritage de Templates

#### Template de base (`base.html`)
```django
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Mon Site Django{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
    {% include 'partes/navbar.html' %}
    
    <main>
        {% block content %}
        {% endblock content %}
    </main>
    
    {% include 'partes/footer.html' %}
</body>
</html>
```

#### Template enfant (`index.html`)
```django
{% extends 'base.html' %}
{% load static %}

{% block title %}Accueil - Mon Site{% endblock %}

{% block content %}
<div class="hero">
    <h1>Bienvenue sur mon site Django!</h1>
    <p>Découvrez les fonctionnalités de notre application.</p>
    
    <div class="actions">
        <a href="{% url 'admin:index' %}" class="btn btn-primary">
            📊 Administration
        </a>
        <a href="{% url 'persons' %}" class="btn btn-secondary">
            👥 Liste des Personnes
        </a>
    </div>
</div>
{% endblock %}
```

### 📦 Inclusions
```django
<!-- Inclusion de composants -->
{% include 'partes/navbar.html' %}
{% include 'partes/footer.html' %}

<!-- Inclusion avec variables -->
{% include 'components/card.html' with title="Mon Titre" content="Mon contenu" %}
```

### 🔒 Sécurité dans les Templates
Django échappe automatiquement les caractères dangereux :
```django
<!-- Sécurisé par défaut -->
{{ user_input }}  <!-- Les balises HTML sont échappées -->

<!-- Pour afficher du HTML brut (dangereux!) -->
{{ trusted_html|safe }}

<!-- Avec le filtre escape -->
{{ potentially_dangerous|escape }}
```

---

## 🌐 URLs et Routage

Le système d'URLs de Django mappe les URLs aux vues correspondantes.

### 🎯 URLs Principales (`project/urls.py`)
```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),           # Interface d'administration
    path('', include('pages.urls')),           # URLs de l'app pages
    path('Person/', include('person.urls')),   # URLs de l'app person
]

# Servir les fichiers médias en développement
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 📱 URLs d'Application (`person/urls.py`)
```python
from django.urls import path
from . import views

app_name = 'person'  # Namespace pour éviter les conflits

urlpatterns = [
    path('', views.persons, name='list'),           # /Person/
    path('detail/<int:id>/', views.person, name='detail'),  # /Person/detail/1/
    path('create/', views.create_person, name='create'),    # /Person/create/
    path('edit/<int:id>/', views.edit_person, name='edit'), # /Person/edit/1/
]
```

### 🔗 Liens Inverses dans les Templates
```django
<!-- Utilisation des noms d'URLs -->
<a href="{% url 'person:list' %}">Liste des Personnes</a>
<a href="{% url 'person:detail' id=person.id %}">Voir Détail</a>
<a href="{% url 'admin:index' %}">Administration</a>

<!-- Avec des paramètres -->
<a href="{% url 'person:edit' id=person.id %}">Modifier</a>
```

---

## 🛡️ Administration Django

Django fournit une interface d'administration automatique puissante.

### 👤 Création d'un Superutilisateur
```bash
python manage.py createsuperuser
```

### ⚙️ Configuration de l'Admin (`person/admin.py`)
```python
from django.contrib import admin
from .models import Person, Post

@admin.register(Person)
class PersonAdmin(admin.ModelAdmin):
    list_display = ['first_name', 'last_name', 'age', 'active', 'en_ligne']
    list_filter = ['active', 'en_ligne']
    search_fields = ['first_name', 'last_name']
    list_editable = ['active', 'en_ligne']
    ordering = ['first_name']

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'post_type', 'created_at']
    list_filter = ['post_type', 'created_at']
    search_fields = ['title', 'content']
```

---

## 📁 Fichiers Statiques et Médias

### 🎨 Fichiers Statiques (CSS, JS, Images)
```python
# settings.py
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, "static")  # Destination (production)
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "project/static")    # Source (développement)
]
```

### 📸 Fichiers Médias (Uploads)
```python
# settings.py
MEDIA_URL = '/photos/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'photos')
```

### 📝 Utilisation dans les Templates
```django
{% load static %}

<!-- Fichiers statiques -->
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/script.js' %}"></script>
<img src="{% static 'images/logo.png' %}" alt="Logo">

<!-- Fichiers médias -->
<img src="{{ person.image.url }}" alt="Photo de {{ person.first_name }}">
```

---

## 🗄️ Base de Données

### 🔧 Configuration MySQL
```python
# Installation des dépendances
pip install mysql-connector-python

# Configuration dans settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django_db',
        'USER': 'root',
        'PASSWORD': 'islam',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

### 🏗️ Création de la Base de Données
```python
# database.py - Script de création
import mysql.connector

# Connexion au serveur MySQL
connection = mysql.connector.connect(
    host='localhost',
    user='root',
    password='islam'
)

cursor = connection.cursor()

# Création de la base de données
cursor.execute("CREATE DATABASE IF NOT EXISTS django_db")
print("Base de données créée avec succès!")

connection.close()
```

### 📊 Commandes de Migration
```bash
# Créer les fichiers de migration
python manage.py makemigrations

# Appliquer les migrations
python manage.py migrate

# Créer une migration vide
python manage.py makemigrations --empty person

# Voir le SQL généré
python manage.py sqlmigrate person 0001

# Revenir à une migration précédente
python manage.py migrate person 0001
```

---

## 🚀 Commandes Django Essentielles

### 📋 Commandes de Développement
```bash
# Démarrer le serveur de développement
python manage.py runserver

# Démarrer sur un port spécifique
python manage.py runserver 8080

# Créer une nouvelle application
python manage.py startapp nom_app

# Ouvrir le shell Django
python manage.py shell

# Collecter les fichiers statiques
python manage.py collectstatic

# Créer un superutilisateur
python manage.py createsuperuser

# Vider la base de données
python manage.py flush

# Charger des données (fixtures)
python manage.py loaddata fixtures.json

# Exporter des données
python manage.py dumpdata person.Person > person_data.json
```

### 🧪 Tests et Debugging
```bash
# Lancer les tests
python manage.py test

# Tests avec couverture
python manage.py test --debug-mode

# Vérifier la configuration
python manage.py check

# Voir les URLs disponibles
python manage.py show_urls
```

---

## 🔐 Bonnes Pratiques de Sécurité

### 🛡️ Configuration de Production
```python
# settings.py pour la production
DEBUG = False
ALLOWED_HOSTS = ['votre-domaine.com', 'www.votre-domaine.com']

# Clé secrète sécurisée
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')

# HTTPS
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# Cookies sécurisés
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

### 🔒 Protection CSRF
```django
<!-- Formulaires avec protection CSRF -->
<form method="post">
    {% csrf_token %}
    <!-- Champs du formulaire -->
</form>
```

---

## 📦 Déploiement

### 🐳 Avec Docker
```dockerfile
# Dockerfile
FROM python:3.11

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

### 🌐 Variables d'Environnement
```python
# settings.py
import os
from dotenv import load_dotenv

load_dotenv()

DEBUG = os.getenv('DEBUG', 'False').lower() == 'true'
SECRET_KEY = os.getenv('SECRET_KEY')
DATABASE_URL = os.getenv('DATABASE_URL')
```

---

## 📚 Ressources et Documentation

### 📖 Documentation Officielle
- [Documentation Django](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Django Channels](https://channels.readthedocs.io/)

### 🛠️ Outils Utiles
- **Django Debug Toolbar** : Debugging avancé
- **Django Extensions** : Commandes utiles supplémentaires
- **Celery** : Tâches asynchrones
- **Redis** : Cache et sessions

### 🎓 Apprentissage
1. Suivez le [tutoriel officiel Django](https://docs.djangoproject.com/fr/4.2/intro/tutorial01/)
2. Pratiquez avec des projets personnels
3. Explorez Django REST Framework pour les APIs
4. Apprenez les tests unitaires avec Django
5. Maîtrisez le déploiement en production

---

## 🎯 Conclusion

Django est un framework puissant qui permet de créer rapidement des applications web robustes et sécurisées. Ce guide couvre les concepts essentiels pour débuter et progresser avec Django.

**Points clés à retenir :**
- Architecture MVT claire et organisée
- ORM puissant pour la gestion des données
- Système de templates flexible avec DTL
- Interface d'administration automatique
- Sécurité intégrée par défaut
- Écosystème riche et communauté active

Continuez à pratiquer et explorez les fonctionnalités avancées pour devenir un expert Django ! 🚀 














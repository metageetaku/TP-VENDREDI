
# Créer et initialiser le dépôt
mkdir ecommerce-platform && cd ecommerce-platform
git init

# Fichier de base
echo "# E-commerce Platform" > README.md
echo "0.1.0" > VERSION
git add README.md VERSION
git commit -m "chore: initial commit with README and VERSION"

# Initialiser GitFlow (répondre aux questions)
git flow init
# Lors de l'init :
# - Branche de production: main
# - Branche de développement: develop
# - Préfixes: feature/, release/, hotfix/, support/, version tag: v

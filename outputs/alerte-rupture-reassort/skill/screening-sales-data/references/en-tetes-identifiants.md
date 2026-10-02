# En-têtes à reconnaître comme identifiants

Point de départ, pas une liste fermée. Juger par le sens : un en-tête absent d'ici peut parfaitement identifier une personne.

## Identité
Nom, Prénom, Nom complet, Client, Cliente, Acheteur, Destinataire, Contact, Titulaire, Membre, Livré à, Facturé à, Customer, Name, Buyer, Recipient

## Coordonnées
E-mail, Email, Mail, Courriel, Téléphone, Tél, Mobile, Portable, Phone

## Localisation
Adresse, Adresse de livraison, Adresse de facturation, Rue, CP, Code postal, Ville, Pays, Address, Shipping address

## Identifiants indirects
N° commande, Numéro de commande, Order ID, Commande, Ticket, N° ticket, Carte fidélité, N° adhérent, Compte client, Customer ID, Session, IP

## Cas limites — demander plutôt que supposer
- `Réf` : souvent la référence produit, parfois la référence commande
- `Source`, `Canal`, `Origine` : souvent le canal de vente, parfois un identifiant de campagne rattaché à une personne
- `Vendeur`, `Caissier`, `Opérateur` : identifie un salarié. C'est une donnée personnelle, même interne
- Une colonne de dates à la minute près sur un petit volume peut réidentifier un acheteur. Le signaler si elle accompagne d'autres colonnes fines

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct transaction {
    float montant;
    char expediteur[50];
    char destinataire[50];
    struct transaction *suivante;
};


struct cartebancaire {
    int numeroCarte;
    char titulaire[50];
    int moisExpiration;
    int anneeExpiration;
    int codeSecurite;
    struct cartebancaire *suivante;
};

struct comptebancaire {
	
    int rib;
    char titulaire[50];
    float solde;
    struct comptebancaire *suivante;
    struct transaction *historique;
    struct cartebancaire *carte;
};

void affichermenu() {
    printf("\n1. Gestion des comptes\n");
    printf("2. Afficher et gérer les transactions bancaires\n");
    printf("3. Afficher la liste des clients\n");
    printf("4. Gestion des cartes bancaires\n");
    printf("5. Satistiques\n");
    printf("0. Quitter\n");
    printf("Choisissez une option\n");
}

void menucomptes() {
    printf("\n1. Créer un compte\n");
    printf("2. Supprimer un compte\n");
    printf("3. Mettre à jour un compte\n");
    printf("4. Afficher les détails d'un compte\n");
    printf("5. Afficher la liste des comptes\n");
    printf("0. Quitter\n");
    printf("Choisissez une option : ");
}

void menutransaction() {
	printf("\n1.Afficher Historique de transactions\n") ;
	printf("2. ajouter transaction\n") ;
	printf("0. Quitter\n");
    printf("Choisissez une option : ");
	
}

void gestioncartes () {
	printf("\n1. Créer une carte bancaire \n") ;
	printf("2. Afficher les détails de la carte :\n") ;
	printf("3. Changer le code PIN  :\n") ;
	printf("0. Quitter\n") ;
    printf("Choisissez une option : ") ;
	
}

void statistique() {
	printf("\n1. Nombre total de comptes bancaires dans le système \n") ;
	printf("2. Total des soldes de tous les comptes \n") ;
	printf("3. Moyenne des soldes des comptes \n") ;
	printf("4. Montant total des transactions effectuées. \n") ;
	printf("0. Quitter\n");
    printf("Choisissez une option : ");
	
	
}

struct comptebancaire *creerCompteAvecAjout(struct comptebancaire *liste, int rib, const char *titulaire) {
    struct comptebancaire *nouveauCompte = (struct comptebancaire *)malloc(sizeof(struct comptebancaire));
    if (nouveauCompte != NULL) {
        nouveauCompte->rib = rib;
        strcpy(nouveauCompte->titulaire, titulaire);
        nouveauCompte->solde = 0.0;
        nouveauCompte->suivante = NULL;
        nouveauCompte->historique = NULL;
        nouveauCompte->carte = NULL;

        if (liste == NULL) {
            liste = nouveauCompte;
        } else {
            struct comptebancaire *courant = liste;
            while (courant->suivante != NULL) {
                courant = courant->suivante;
            }
            courant->suivante = nouveauCompte;
        }
    }
    return liste;
}

void afficherListeComptes(struct comptebancaire *liste) {
    struct comptebancaire *courant = liste;
    while (courant != NULL) {
        printf("RIB : %d\n", courant->rib);
        printf("Titulaire : %s\n", courant->titulaire);
        printf("Solde : %.2f\n", courant->solde);
        printf("--------------------------\n");
        courant = courant->suivante;
    }
}

struct comptebancaire *supprimercompte(struct comptebancaire *liste, int ribASupprimer) {
    if (liste == NULL) {
        printf("Aucun compte à supprimer.\n");
        return NULL;
    }

    if (liste->rib == ribASupprimer) {
        struct comptebancaire *nouvelleListe = liste->suivante;
        struct transaction *couranteTransaction = liste->historique;
        while (couranteTransaction != NULL) {
            struct transaction *suivanteTransaction = couranteTransaction->suivante;
            free(couranteTransaction);
            couranteTransaction = suivanteTransaction;
        }
        struct cartebancaire *carteASupprimer = liste->carte;
        free(liste);
        while (carteASupprimer != NULL) {
            struct cartebancaire *carteSuivante = carteASupprimer->suivante;
            free(carteASupprimer);
            carteASupprimer = carteSuivante;
        }
        printf("Compte supprimé avec succès!\n");
        return nouvelleListe;
    }

    struct comptebancaire *courant = liste;
    while (courant->suivante != NULL && courant->suivante->rib != ribASupprimer) {
        courant = courant->suivante;
    }

    if (courant->suivante == NULL) {
        printf("Compte non trouvé.\n");
    } else {
        struct comptebancaire *compteASupprimer = courant->suivante;
        struct transaction *couranteTransaction = compteASupprimer->historique;
        while (couranteTransaction != NULL) {
            struct transaction *suivanteTransaction = couranteTransaction->suivante;
            free(couranteTransaction);
            couranteTransaction = suivanteTransaction;
        }
        struct cartebancaire *carteASupprimer = compteASupprimer->carte;
        courant->suivante = courant->suivante->suivante;
        free(compteASupprimer);
        while (carteASupprimer != NULL) {
            struct cartebancaire *carteSuivante = carteASupprimer->suivante;
            free(carteASupprimer);
            carteASupprimer = carteSuivante;
        }
        printf("Compte supprimé avec succès!\n");
    }

    return liste;
}

void mettreajourcompte(struct comptebancaire *liste, int ribAMettreAJour, float nouveauSolde) {
    struct comptebancaire *courant = liste;
    while (courant != NULL && courant->rib != ribAMettreAJour) {
        courant = courant->suivante;
    }

    if (courant == NULL) {
        printf("Compte non trouvé.\n");
    } else {
        courant->solde = nouveauSolde;
        printf("Compte mis à jour avec succès!\n");
    }
}

void afficherDetailsCompte(struct comptebancaire *liste, int ribAChercher) {
    struct comptebancaire *courant = liste;
    while (courant != NULL && courant->rib != ribAChercher) {
        courant = courant->suivante;
    }

    if (courant == NULL) {
        printf("Compte non trouvé.\n");
    } else {
        printf("RIB : %d\n", courant->rib);
        printf("Titulaire : %s\n", courant->titulaire);
        printf("Solde : %.2f\n", courant->solde);
        printf("Historique des transactions :\n");
        struct transaction *couranteTransaction = courant->historique;
        while (couranteTransaction != NULL) {
            printf("Montant : %.2f\n", couranteTransaction->montant);
            printf("Expéditeur : %s\n", couranteTransaction->expediteur);
            printf("Destinataire : %s\n", couranteTransaction->destinataire);
            printf("--------------------------\n");
            couranteTransaction = couranteTransaction->suivante;
        }
        printf("Carte associée :\n");
        if (courant->carte != NULL) {
            printf("Numéro de carte : %s\n", courant->carte->numeroCarte);
            printf("Titulaire de la carte : %s\n", courant->carte->titulaire);
            printf("Date d'expiration : %02d/%d\n", courant->carte->moisExpiration, courant->carte->anneeExpiration);
            printf("Code de sécurité : %d\n", courant->carte->codeSecurite);
        } else {
            printf("Aucune carte associée.\n");
        }
        printf("--------------------------\n");
    }
}

void afficherHistorique(struct transaction *historique) {
    struct transaction *courant = historique;
    while (courant != NULL) {
        printf("Montant : %.2f\n", courant->montant);
        printf("Expéditeur : %s\n", courant->expediteur);
        printf("Destinataire : %s\n", courant->destinataire);
        printf("--------------------------\n");
        courant = courant->suivante;
    }
}

void ajouterTransaction(struct comptebancaire *listeComptes, int ribExpediteur, int ribDestinataire, float montant) {
    struct comptebancaire *compteExpediteur = NULL;
    struct comptebancaire *compteDestinataire = NULL;

    struct comptebancaire *courant = listeComptes;

    while (courant != NULL) {
        if (courant->rib == ribExpediteur) {
            compteExpediteur = courant;
            break;
        }
        courant = courant->suivante;
    }

    courant = listeComptes;

    while (courant != NULL) {
        if (courant->rib == ribDestinataire) {
            compteDestinataire = courant;
            break;
        }
        courant = courant->suivante;
    }

    if (compteExpediteur == NULL || compteDestinataire == NULL) {
        printf("Compte expéditeur ou destinataire non trouvé.\n");
        return;
    }

    if (compteExpediteur->solde < montant) {
        printf("Solde insuffisant pour effectuer la transaction.\n");
        return;
    }

    compteExpediteur->solde -= montant;
    compteDestinataire->solde += montant;

    struct transaction *nouvelleTransaction = (struct transaction *)malloc(sizeof(struct transaction));
    if (nouvelleTransaction != NULL) {
        nouvelleTransaction->montant = montant;
        strcpy(nouvelleTransaction->expediteur, compteExpediteur->titulaire);
        strcpy(nouvelleTransaction->destinataire, compteDestinataire->titulaire);
        nouvelleTransaction->suivante = compteExpediteur->historique;
        compteExpediteur->historique = nouvelleTransaction;
        printf("Transaction effectuée avec succès!\n");
    }
}

void creerCarte(struct cartebancaire **listeCartes, int numero, const char *titulaire, int moisExpiration, int anneeExpiration) {
    struct cartebancaire *nouvelleCarte = (struct cartebancaire *)malloc(sizeof(struct cartebancaire));
    if (nouvelleCarte != NULL) {
        nouvelleCarte->numeroCarte = numero;
        strcpy(nouvelleCarte->titulaire, titulaire);
        nouvelleCarte->moisExpiration = moisExpiration;
        nouvelleCarte->anneeExpiration = anneeExpiration;
        nouvelleCarte->codeSecurite = 0000;  

        nouvelleCarte->suivante = *listeCartes;
        *listeCartes = nouvelleCarte;
    }
}

 
void afficherNomsComptes(struct comptebancaire *liste) {
    struct comptebancaire *courant = liste;
    
    printf("Liste des noms des comptes bancaires :\n");

    while (courant != NULL) {
        printf("- %s\n", courant->titulaire);
        courant = courant->suivante;
    }
}

void afficherDetailsCarte(struct cartebancaire *listeCartes, int numeroRecherche) {
    struct cartebancaire *courant = listeCartes;
    while (courant != NULL && courant->numeroCarte != numeroRecherche) {
        courant = courant->suivante;
    }

    if (courant != NULL) {
        printf("Détails de la carte bancaire :\n");
        printf("Numéro de carte : %d\n", courant->numeroCarte);
        printf("Titulaire : %s\n", courant->titulaire);
        printf("Mois d'expiration : %d\n", courant->moisExpiration);
        printf("Année d'expiration : %d\n", courant->anneeExpiration);
        printf("Code de sécurité : %d\n", courant->codeSecurite);
    } else {
        printf("Carte non trouvée.\n");
    }
}


void changerCodePIN(struct cartebancaire *listeCartes, int numeroRecherche, int nouveauCode) {
    struct cartebancaire *courant = listeCartes;
    while (courant != NULL && courant->numeroCarte != numeroRecherche) {
        courant = courant->suivante;
    }

    if (courant != NULL) {
        courant->codeSecurite = nouveauCode;
        printf("Le code PIN a été changé avec succès.\n");
    } else {
        printf("Carte non trouvée.\n");
    }
}

int nombreTotalComptes(struct comptebancaire *liste) {
    int nombreComptes = 0;
    
    
    struct comptebancaire *courant = liste;
    while (courant != NULL) {
        nombreComptes++;
        courant = courant->suivante;
    }

    return nombreComptes;
}

float calculerTotalSoldes(struct comptebancaire *liste) {
    float totalSoldes = 0.0;

    struct comptebancaire *courant = liste;
    while (courant != NULL) {
        totalSoldes += courant->solde;
        courant = courant->suivante;
    }

    return totalSoldes;
}

float calculerMoyenneSoldes(struct comptebancaire *liste) {
    float totalSoldes = calculerTotalSoldes(liste);

    int nombreComptes = nombreTotalComptes(liste);

    float moyenneSoldes = (nombreComptes > 0) ? (totalSoldes / nombreComptes) : 0.0;

    return moyenneSoldes;
}

float calculerMontantTotalTransactions(struct transaction *listeTransactions) {
    float montantTotal = 0.0;
    struct transaction *courante = listeTransactions;
    while (courante != NULL) {
        montantTotal += courante->montant;
        courante = courante->suivante;
    }
    return montantTotal;
}


   

  
  
  int main() {
    int c, c1, c2, c3, c4, rs, rmj, re, rd, moisexp, anneeexp, nc, mdp, nmdp, cs;
    int rib = 619000000;
    int numcarte = 0000000000000000;
    char titulaire[50], titcarte[50];
    struct comptebancaire *liste = NULL;
    float nouvsolde, transfsolde;
    struct transaction *histor = NULL;
    struct cartebancaire *listecartes = NULL;

    do {
        affichermenu();

        printf("Veuillez choisir une option : ");
        scanf("%d", &c);

        switch (c) {
            case 1:
                do {
                    menucomptes();

                    printf("Veuillez choisir une option : ");
                    scanf("%d", &c1);

                    switch (c1) {
                        case 1:
                            printf("Saisir le nom et le prénom : ");
                            scanf("%s", titulaire);
                            creerCompteAvecAjout(liste, rib, titulaire);
                            rib++;
                            printf("Compte créé avec succès! ");
                            affichermenu();
                            break;

                        case 2:
                            printf("Saisir le rib du compte à supprimer : ");
                            scanf("%d", &rs);
                            supprimercompte(liste, rs);
                            printf("Compte supprimé avec succès!");
                            affichermenu();
                            break;

                        case 3:
                            printf("Saisir le rib du compte à mettre à jour : ");
                            scanf("%d", &rmj);
                            printf("Saisir le nouveau solde : ");
                            scanf("%f", &nouvsolde);
                            mettreajourcompte(liste, rmj, nouvsolde);
                            printf("Compte mis à jour avec succès!");
                            affichermenu();
                            break;

                        case 4:
                            printf("Saisir le rib du compte : ");
                            scanf("%d", &rd);
                            afficherDetailsCompte(liste, rd);
                            affichermenu();
                            break;

                        case 5:
                            afficherListeComptes(liste);
                            affichermenu();
                            break;

                        case 0:
                            affichermenu();
                            break;

                        default:
                            printf("Choix invalide!");
                            affichermenu();
                    }
                } while ((c1 >= 0) && (c1 <= 5));
                break;

            case 2:
                do {
                    menutransaction();

                    printf("Veuillez choisir une option : ");
                    scanf("%d", &c2);

                    switch (c2) {
                        case 1:
                            afficherHistorique(histor);
                            affichermenu();
                            break;

                        case 2:
                            printf("Saisir le RIB de l'expéditeur : ");
                            scanf("%d", &re);

                            printf("Saisir le RIB du destinataire : ");
                            scanf("%d", &rd);
                            printf("Saisir le montant à transférer : ");
                            scanf("%f", &transfsolde);
                            ajouterTransaction(liste, re, rd, transfsolde);
                            affichermenu();
                            break;

                        case 0:
                            affichermenu();
                            break;

                        default:
                            printf("Choix invalide!");
                            affichermenu();
                    }
                } while ((c2 >= 0) && (c2 <= 2));
                break;

            case 3:
                afficherNomsComptes(liste);
                break;

            case 4:
                do {
                    gestioncartes();
                    printf("Veuillez choisir une option : ");
                    scanf("%d", &c3);

                    switch (c3) {
                        case 1:
                            printf("Veuillez saisir le titulaire du compte : ");
                            scanf("%s", titcarte);
                            printf("Veuillez saisir la date d'expiration (mois et année séparés par un espace) : ");
                            scanf("%d %d", &moisexp, &anneeexp);
                            creerCarte(&listecartes, numcarte, titcarte, moisexp, anneeexp);
                            numcarte++;
                            affichermenu();
                            break;

                        case 2:
                            printf("Veuillez saisir le numéro de la carte : ");
                            scanf("%d", &nc);
                            afficherDetailsCarte(listecartes, nc);
                            affichermenu();
                            break;

                        case 3:
                            printf("Veuillez saisir le numéro du carte : ");
                            scanf("%d", &nmdp);
                            printf("Veuillez saisir le nouveau mot de passe : ");
                            scanf("%d", &mdp);
                            changerCodePIN(listecartes, nmdp, mdp);
                            affichermenu();
                            break;

                        case 0:
                            affichermenu();
                            break;

                        default:
                            printf("Choix invalide!");
                            affichermenu();
                    }
                } while ((c3 >= 0) && (c3 <= 3));
                break;

            case 5:
                do {
                    statistique();
                    printf("Veuillez choisir une option : ");
                    scanf("%d", &c4);

                    switch (c4) {
                        case 1:
                            nombreTotalComptes(liste);
                            affichermenu();
                            break;
                        case 2:
                            calculerTotalSoldes(liste);
                            affichermenu();
                            break;
                        case 3:
                            calculerMoyenneSoldes(liste);
                            affichermenu();
                            break;

                        case 4:
                            calculerMontantTotalTransactions(histor);
                            affichermenu();
                            break;
                        default:
                            printf("Choix invalide!");
                            affichermenu();
                    }
                } while ((c4 >= 0) && (c4 <= 4));
                break;

            case 0:
                printf("Au revoir!\n");
                break;
            default:
                printf("Option invalide. Veuillez choisir une option valide.\n");
                break;
        }
    } while ((c >= 0) && (c <= 5));

    return 0;
}

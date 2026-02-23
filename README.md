import java.io.*;
import java.util.Scanner;

public class GestionMagasin {

    static final int MAX = 100;
    static String[] nomsProduits = new String[MAX];
    static double[] prixProduits = new double[MAX];
    static int[] stockProduits = new int[MAX];
    static int totalProduits = 0;

    static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) throws IOException {

        chargerStock();
        int choix = 0;

        while (choix != 7) {
            afficherMenu();
            System.out.print("Votre choix : ");
            choix = Integer.parseInt(scanner.nextLine());

            switch (choix) {
                case 1: ajouterProduit(); break;
                case 2: afficherProduits(); break;
                case 3: rechercherProduitConsole(); break;
                case 4: realiserVente(); break;
                case 5: afficherAlertes(); break;
                case 6:
                    sauvegarderStock();
                    System.out.println("Sauvegarde effectuée !");
                    break;
                case 7:
                    sauvegarderStock();
                    System.out.println("Programme fermé. Données sauvegardées.");
                    break;
                default:
                    System.out.println("Choix invalide !");
            }

            System.out.println("Appuyez sur Entrée pour continuer...");
            scanner.nextLine();
        }
    }

    static void afficherMenu() {
        System.out.println("\n===== GESTION DU MAGASIN =====");
        System.out.println("1. Ajouter produit");
        System.out.println("2. Afficher produits");
        System.out.println("3. Rechercher produit");
        System.out.println("4. Effectuer une vente");
        System.out.println("5. Produits en alerte (<5)");
        System.out.println("6. Sauvegarder");
        System.out.println("7. Quitter");
    }

    static void ajouterProduit() {
        if (totalProduits >= MAX) {
            System.out.println("Stock plein !");
            return;
        }

        System.out.print("Nom du produit : ");
        String nom = scanner.nextLine();

        System.out.print("Prix : ");
        double prix = Double.parseDouble(scanner.nextLine());

        System.out.print("Quantité : ");
        int quantite = Integer.parseInt(scanner.nextLine());

        nomsProduits[totalProduits] = nom;
        prixProduits[totalProduits] = prix;
        stockProduits[totalProduits] = quantite;

        totalProduits++;
        System.out.println("Produit ajouté avec succès.");
    }

    static void rechercherProduitConsole() {
        System.out.print("Nom à rechercher : ");
        String nom = scanner.nextLine();

        int index = rechercherProduit(nom);

        if (index != -1) {
            System.out.println("Produit trouvé : "
                    + nomsProduits[index]
                    + " | Prix : " + prixProduits[index]
                    + " | Stock : " + stockProduits[index]);
        } else {
            System.out.println("Produit non trouvé.");
        }
    }

    static int rechercherProduit(String nom) {
        for (int i = 0; i < totalProduits; i++) {
            if (nomsProduits[i].equalsIgnoreCase(nom)) {
                return i;
            }
        }
        return -1;
    }

    static void realiserVente() {
        System.out.print("Nom du produit : ");
        String nom = scanner.nextLine();

        int index = rechercherProduit(nom);

        if (index == -1) {
            System.out.println("Produit introuvable !");
            return;
        }

        System.out.print("Quantité vendue : ");
        int quantite = Integer.parseInt(scanner.nextLine());

        if (quantite > stockProduits[index]) {
            System.out.println("Stock insuffisant !");
            return;
        }

        double montant = prixProduits[index] * quantite;

        if (montant > 1000) {
            montant *= 0.9;
            System.out.println("Réduction de 10% appliquée !");
        }

        stockProduits[index] -= quantite;

        System.out.println("\n===== TICKET =====");
        System.out.println("Produit : " + nomsProduits[index]);
        System.out.println("Quantité : " + quantite);
        System.out.println("Prix unitaire : " + prixProduits[index]);
        System.out.println("Total : " + montant);
    }

    static void afficherProduits() {

        if (totalProduits == 0) {
            System.out.println("Aucun produit disponible.");
            return;
        }

        System.out.println("\nNOM\tPRIX\tSTOCK\tVALEUR");

        for (int i = 0; i < totalProduits; i++) {
            double valeur = prixProduits[i] * stockProduits[i];
            System.out.println(nomsProduits[i] + "\t"
                    + prixProduits[i] + "\t"
                    + stockProduits[i] + "\t"
                    + valeur);
        }
    }

    static void afficherAlertes() {

        boolean alerte = false;
        System.out.println("\nProduits en stock faible :");

        for (int i = 0; i < totalProduits; i++) {
            if (stockProduits[i] < 5) {
                System.out.println(nomsProduits[i]
                        + " | Quantité : " + stockProduits[i]);
                alerte = true;
            }
        }

        if (!alerte) {
            System.out.println("Aucune alerte.");
        }
    }

    static void sauvegarderStock() throws IOException {

        PrintWriter writer = new PrintWriter(new FileWriter("stock.txt"));

        for (int i = 0; i < totalProduits; i++) {
            writer.println(nomsProduits[i] + ";"
                    + prixProduits[i] + ";"
                    + stockProduits[i]);
        }

        writer.close();
    }

    static void chargerStock() throws IOException {

        File fichier = new File("stock.txt");
        if (!fichier.exists()) return;

        BufferedReader reader = new BufferedReader(new FileReader(fichier));
        String ligne;

        while ((ligne = reader.readLine()) != null && totalProduits < MAX) {

            String[] parties = ligne.split(";");

            if (parties.length == 3) {
                nomsProduits[totalProduits] = parties[0];
                prixProduits[totalProduits] = Double.parseDouble(parties[1]);
                stockProduits[totalProduits] = Integer.parseInt(parties[2]);
                totalProduits++;
            }
        }

        reader.close();
    }
}

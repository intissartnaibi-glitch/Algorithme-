# Algorithme-


Exercice 1 — 

Création d'une liste aléatoire
Création d'une liste chaînée de n éléments avec des valeurs aléatoires entre 0 et max. Chaque nouvel élément
est inséré en tête.



#include <stdio.h>
#include <stdlib.h>
#include <time.h>
struct Element {
 int val;
 struct Element *suivant;
};
typedef struct Element LISTE;
LISTE* CreerListeAleatoire(int n, int max) {
 LISTE *L = NULL;
 srand(time(NULL));
 for (int i = 0; i < n; i++) {
 LISTE *nouveau = (LISTE*)malloc(sizeof(LISTE));
 if (!nouveau) { printf("Erreur d'allocation\n"); return L; }
 nouveau->val = rand() % (max + 1);
 nouveau->suivant = L;
 L = nouveau;
 }
 return L;
}
void AfficherListe(LISTE *L) {
 while (L) { printf("%d -> ", L->val); L = L->suivant; }
 printf("NULL\n");
}



Complexité : O(n) — une allocation par élément.




7.2 Exercice 2 

— Recherche d'une valeur
Parcours séquentiel de la liste jusqu'à trouver la valeur cible ou atteindre la fin.



int RechercherValeur(LISTE *L, int valeur) {
 LISTE *ptr = L;
 while (ptr != NULL) {
 if (ptr->val == valeur)
 return 1; // trouvé
 ptr = ptr->suivant;
 }
 return 0; // non trouvé
}



Complexité : O(n) dans le pire cas (valeur absente ou en fin de liste).



7.3 Exercice 3 


— Suppression de la valeur minimale
On parcourt la liste pour repérer le minimum, puis on met à jour les pointeurs pour l'exclure avant de libérer la 



int SupprimerMin(LISTE **L) {
 if (*L == NULL) return 0;
 if ((*L)->suivant == NULL) { free(*L); *L = NULL; return 1; }
 LISTE *ptr = *L, *precMin = NULL, *minPtr = *L;
 int minVal = (*L)->val;
 while (ptr->suivant) {
 if (ptr->suivant->val < minVal) {
 minVal = ptr->suivant->val;
 precMin = ptr;
 minPtr = ptr->suivant;
 }
 ptr = ptr->suivant;
 }
 if (precMin == NULL)
 *L = minPtr->suivant;
 else
 precMin->suivant = minPtr->suivant;
 free(minPtr);
 return 1;
}





Complexité : O(n) — un seul parcours de la liste.





7.4 Exercice 4 —



 Fusion de deux listes en alternance
On construit une nouvelle liste en intercalant un élément de L1, puis un de L2, jusqu'à épuisement de l'une des
deux. Le reste est ajouté à la fin.



void InsererEnFin(LISTE **L, int val) {
 LISTE *nouveau = (LISTE*)malloc(sizeof(LISTE));
 nouveau->val = val; nouveau->suivant = NULL;
 if (*L == NULL) { *L = nouveau; return; }
 LISTE *ptr = *L;
 while (ptr->suivant) ptr = ptr->suivant;
 ptr->suivant = nouveau;
}
LISTE* FusionAlternance(LISTE *L1, LISTE *L2) {
 LISTE *resultat = NULL;
 LISTE *p1 = L1, *p2 = L2;
 while (p1 && p2) {
 InsererEnFin(&resultat, p1->val);
 InsererEnFin(&resultat, p2->val);
 p1 = p1->suivant;
 p2 = p2->suivant;
 }
 while (p1) { InsererEnFin(&resultat, p1->val); p1 = p1->suivant; }
 while (p2) { InsererEnFin(&resultat, p2->val); p2 = p2->suivant; }
 return resultat;
}



Complexité : 



O((n+m)²) à cause de InsererEnFin — optimisable en gardant un pointeur sur la fin.




7.5 Exercice 5 —



 Destruction d'une liste
Libération de tous les noeuds un à un en avançant avec un pointeur temporaire.



void DetruireListe(LISTE **L) { LISTE *ptr;
 while (*L != NULL) {
 ptr = *L;
 *L = (*L)->suivant;
 free(ptr);
 }
}
Complexité : 


O(n) — chaque noeud est libéré exactement une fois.



7.6 Exercice 6 


— Piles : séparation pairs/impairs
On dépile P1 et on répartit les éléments dans P2 (pairs) et P3 (impairs), puis on transfère P3 dans P2 pour
obtenir une pile unifiée pairs-puis-impairs.



typedef struct stack { int data; struct stack *next; } STACK;
void push(STACK **head, int value) {
 STACK *node = (STACK*)malloc(sizeof(STACK));
 if (!node) abort();
 node->data = value; node->next = *head; *head = node;
}
int pop(STACK **head) {
 if (*head == NULL) abort();
 STACK *top = *head;
 int value = top->data;
 *head = top->next;
 free(top);
 return value;
}
STACK* PairImpair(STACK *P1) {
 STACK *P2 = NULL, *P3 = NULL;
 int val;
 while (P1) {
 val = pop(&P1);
 if (val % 2 == 0) push(&P2, val);
 else push(&P3, val);
 }
 while (P3) { val = pop(&P3); push(&P2, val); }
 return P2;
}



Complexité : O(n) — chaque élément est manipulé un nombre constant de fois.



7.7 Exercice 7 —


 Files et détection de palindrome
On place la première moitié de la chaîne dans une pile (LIFO), et on compare lettre par lettre avec la seconde
moitié défilée depuis la file (FIFO). Si toutes les paires correspondent, c'est un palindrome.



struct queue_node { char data; struct queue_node *next; };
struct queue { struct queue_node *first; struct queue_node *last; };
void enqueue(struct queue *q, char value) {
 struct queue_node *node = malloc(sizeof(struct queue_node));
 node->data = value; node->next = NULL;
 if (!q->first) q->first = q->last = node;
 else { q->last->next = node; q->last = node; }
}
int dequeue(struct queue *q, char *value) {
 if (!q->first) return -1; *value = q->first->data;
 struct queue_node *tmp = q->first;
 q->first = q->first->next;
 if (!q->first) q->last = NULL;
 free(tmp); return 0;
}
struct queue* EcrireMessage(char chaine[]) {
 struct queue *q = malloc(sizeof(struct queue));
 q->first = q->last = NULL;
 for (int i = 0; chaine[i] != '\0'; i++) enqueue(q, chaine[i]);
 return q;
}
int EstPalindrome(char chaine[]) {
 int len = strlen(chaine), moitie = len / 2;
 struct queue *Q = EcrireMessage(chaine);
 STACK *p = NULL;
 char c, c1, c2;
 for (int i = 0; i < moitie; i++) { dequeue(Q, &c); push(&p, c); }
 if (len % 2 != 0) dequeue(Q, &c); // sauter le milieu
 for (int i = 0; i < moitie; i++) {
 dequeue(Q, &c1); c2 = pop(&p);
 if (c1 != c2) return 0;
 }
 return 1;
}



Complexité : O(n) en temps et en espace.
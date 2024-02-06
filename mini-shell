#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <errno.h>
#include <stdbool.h> 

#define MAXLINE 100
#define MAXARGS 16


// vérifier le nombre d'arguments entrées (argc)
int parse_line(char *s, char *argv[]){
        int count = 0;
        char *spaceEnterTab = " \n\t";

        argv[count] = strtok(s, spaceEnterTab); 
	//coupe des mots dans la commande que l'utilisateur saisi qui sont séparés par des ponctuations dans spaceEnterTab

        while ((argv[count] != NULL) && (count+1 < MAXARGS)){
            argv[++count] = strtok((char *) 0, spaceEnterTab);
	//copier chaque valeur de la commande en mettant à chaque tableau argv
        }
        //printf("Count =%d\n", count);
        return count;
}

/* ----------- MAIN PROGRAM ------------ */
int main(void) {
    
    char cmdline[MAXLINE]; //buffer pour fgets
    char *argv[MAXARGS];
    int argc;
    int status;
    int i, j, k, m;
    pid_t pid;
    int fd[2];
    pipe(fd);

    printf("<============================MINISHELL============================>\n");

// créer boucle sans arrêt, ce qui va excécuter jusqu'à quand l'utiliser taper "exit".
    while(1){
        printf("\n");
        printf("minishell$> "); // afficher le nom du terminal

        fgets(cmdline, MAXLINE, stdin); // fgets pour lire les commandes dans le shell, prendre tous ce que l'utilisateur tape

        argc = parse_line(cmdline, argv); //cette fonction va vérifier le nombre d'arguments que l'utilisateur a saisi 
	// ils sont séparés par les espaces, tabulation ou retour à la ligne 

// taper exit, le minishell va s'arrêter
        if(strcmp(argv[0], "exit") == 0){
            printf("A bientôt!\n");
            exit(1);
        }

        // si argv[0] vaut "cd" exécuter la commande cd
        if(strcmp(argv[0], "cd") == 0){
		//si on tape "cd" sans autres arguments supplémentaires, c'est pour se rendre dans le répertoire HOME, on peut utiliser pwd pour vérifier emplacement 

            if(argv[1] == NULL){
                chdir(getenv("HOME")); // getenv ->  retourner le PATH qui emmene à le dossier qu'on (ici est HOME) 
                //chdir + lien du dossier pour aller au dossier de destination
		continue;
            } else {
                // si le lien est faux, le fichier et le dossier n'existent pas, afficher erreur
                if(chdir(argv[1]) == -1){
                    printf("bash: cd: %s: No such file or directory\n", argv[1]);
                }
                continue;
            }
        }
	
	//si les utilisateur ne saisissent rien et juste tapent enter, le programme continue exécuter sans rien faire
        if(argc == 0) {
            continue;

        } else {
            pid = fork();
	
		//quand fork () retourne à négative, ne creer pas le processus
            if(pid < 0){
                perror("Fork erreur!");
                exit(EXIT_FAILURE);
            }            
          
            if(pid == 0){ //child process 
		// quand fork() return 1
                int out;
     
                out = execvp(argv[0],argv); // exécuter la commande saisi avec la fonction execvp

                if (out < 0) { // l'exécution de execvp() rencontre des faux, afficher erreur
                    fprintf(stderr, "Exec erreur.\n");
                    exit(0);
                }
            } else { //Parent, shell continue
                if (wait(&status) == -1){
                    perror("Parent fork erreur!!\n");
                } else {
                    wait(&pid);
                }
            }
        }
    }     
}

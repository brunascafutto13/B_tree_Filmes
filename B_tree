#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define ordem 4

typedef struct Node {
    int n;            //Numero de filmes atualmente presentes no no
    char* keys[ordem];//Vetor com todas as chaves de cada no
    int index[ordem]; //Vetor com todos os indices de cada filme do no (usado para a busca no arquivo)
    struct Node* children[ordem + 1]; //Ponteiros para os filhos
    struct Node* parent; //Ponteiro para o pai do no
} Node;

//Criação de um novo nó vazio
Node* create() {
    Node* newNode = (Node*)malloc(sizeof(Node)); //Alocacao do no
    // Adiciona todas as informacoes do no como vazia
    newNode->n = 0; 
    newNode->parent = NULL;
    for (int i = 0; i < ordem ; i++) {
        newNode->keys[i] = NULL;
        newNode->index[i] = 0;
        newNode->children[i] = NULL;
    }
    newNode->children[ordem] = NULL; //(numero de filhos =ordem+1)
    return newNode;
}


//Inserção de uma nova chave
void insertKey(Node* node,  char* key, int index) {
    int i = node->n - 1;
   //Busca a posiçao certa para realizar a inserção
    while (i >= 0 && strcmp(node->keys[i], key) > 0) {
        node->keys[i + 1] = node->keys[i];
        node->index[i + 1] = node->index[i];
        i--;
    }
    //Inserção da chave na posiçao correta
    node->keys[i + 1] = strdup(key);
    node->index[i + 1] = index;
    // Atualiza o número corrente de filmes no no
    node->n++;
}

//Divisão dos filhos
void split_child(Node* parent, int index) {
    Node* child = parent->children[index];
    Node* newNode = create();
  
//Loop para vidir os nós filhos na metade
    for (int i = ordem / 2 + 1; i < ordem; i++) {
      //Insere chaves e filhos no novo no
        insertKey(newNode, child->keys[i], child->index[i]);
        newNode->children[newNode->n] = child->children[i];
        if (newNode->children[newNode->n] != NULL) {
            newNode->children[newNode->n]->parent = newNode;
        }
        child->keys[i] = NULL;
        child->index[i] = 0;
        child->children[i] = NULL;
        child->n--; //Diminui o numero de palavras no filho (passou para novo no)
    }
    child->n--;
    for (int i = parent->n; i >= index + 1; i--) {
        parent->children[i + 1] = parent->children[i];
    }
    parent->children[index + 1] = newNode;
    newNode->parent = parent;
    insertKey(parent, child->keys[ordem / 2], child->index[ordem / 2]);
    child->keys[ordem / 2] = NULL;
    child->index[ordem / 2] = 0;
}

void insert_not_full(Node* node, char* key, int index) {
    int i = node->n - 1;
   //ainda não tem filhos -Insere no próprio no
    if (node->children[0] == NULL) {
        insertKey(node, key, index);
        return;
    }
   //Encontra a posição correta para inserção
    while (i >= 0 && strcmp(key, node->keys[i]) <= 0) {
        i--;
    }
    i++;
    //Check se o filho esta cheio
    if (node->children[i]->n == ordem) {
        split_child(node, i);
        if (strcmp(key, node->keys[i]) > 0) {
            i++;
        }
    }
    insert_not_full(node->children[i], key, index);
}

//Função de inserção
void insert(Node** root,  char* key, int index) {
  //Arvore vazia, criação de um novo no e inserção da chave
    if (*root == NULL) {
        *root = create();
        insertKey(*root, key, index);
  //Arvore possui pelo menos 1 no
    } else {
      //Se o no ja estiver cheio,fazer a divisao
      if ((*root)->n == ordem) {    
            Node* new_root = create();
            new_root->children[0] = *root;
            (*root)->parent = new_root;
            split_child(new_root, 0);
            int i = 0;
            if (strcmp(key, new_root->keys[0]) > 0) {
                i++;
            }
           //Realiza a inserção
            insert_not_full(new_root->children[i], key, index);
            *root = new_root;
        } else {
            insert_not_full(*root, key, index);
        }
    }
}

void searchPrefix(Node* node,  char* prefix) {
    if (node != NULL) {
        int i = 0;
    //Procura no nó passado pela funcao
        while (i < node->n && strncmp(node->keys[i], prefix, strlen(prefix)) < 0) {
            i++;
        }
        //encontra as palavras com esse prefixo
        while (i < node->n && strncmp(node->keys[i], prefix, strlen(prefix)) == 0) {
            printf("%s - %d\n", node->keys[i], node->index[i]);
            i++;
        }
        //Procura nos filhos
        if (node->children[i] != NULL) {
            searchPrefix(node->children[i], prefix);
        }
    }
}

//Busca pelo nome completo do filme
int search(Node* node, char* movieName) {
    if (node != NULL) {
        int i = 0;
        while (i < node->n && strncmp(node->keys[i], movieName, strlen(movieName)) < 0) {
            i++;
        }

        if (i < node->n && strncmp(node->keys[i], movieName, strlen(movieName)) == 0) {
            
            return node->index[i]; // Retorna o índice da chave com o prefixo desejado
        }

        if (node->children[i] != NULL) {
            return search(node->children[i], movieName); // Busca recursivamente nos filhos
        }
    }
  else{
    printf("Palavra não encontrada");
    return -1; // Retorno -1 se a palavra não for encontrada
  }
}



void removeKey(Node* node, int index) {
    free(node->keys[index]);
    for (int i = index; i < node->n - 1; i++) {
        node->keys[i] = node->keys[i + 1];
        node->index[i] = node->index[i + 1];
    }
    node->keys[node->n - 1] = NULL;
    node->index[node->n - 1] = 0;
    node->n--;
}


void remove_from_node(Node* node,  char* key) {
    Node *predecessor;
    int i = 0;
    //encontra a posição correta do filme no no
    while (i < node->n && strcmp(node->keys[i], key) < 0) {
        i++;
    }
   //Encontrou a chave no nó, faz a remoção
    if (i < node->n && strcmp(node->keys[i], key) == 0) {
        if (node->children[0] == NULL) {
            removeKey(node, i);
        } else {
              while (node->children[node->n] != NULL) {
         predecessor = predecessor->children[node->n];
    }   
          //Organiza para os filhos do removido serem filhos de seus prodecessores
            Node* predecessor_child = predecessor->children[i];
            char* predecessor_key = node->keys[i];
            int predecessor_value = node->index[i];
            node->keys[i] = strdup(predecessor_child->keys[predecessor_child->n - 1]);
            node->index[i] = predecessor_child->index[predecessor_child->n - 1];
            removeKey(predecessor_child, predecessor_child->n - 1);
            free(predecessor_key);
        }
    }
  //Não encontrou a chave no nó, faz a busca no proximo

    if (node->children[0] != NULL) {
        remove_from_node(node->children[i], key);
    }
}

void remove_from_tree(Node* root,  char* key) {
  //Chave não presente na árvore
    if (root == NULL) {
        printf("Chave não encontrada: %s\n", key);
        return;
    }
   //Chave presente na arvore
    remove_from_node(root, key);
    if ((root)->n == 0 && (root)->children[0] != NULL) {
        Node* new_root = (root)->children[0];
        free(root);
        root = new_root;
        (root)->parent = NULL;
    }
}


void print_tree(Node* node, int level) {
    if (node != NULL) {
        for (int i = 0; i < node->n; i++) {
            print_tree(node->children[i], level + 1);
            printf("%*s%s - %d\n", level * 4, "", node->keys[i], node->index[i]);
        }
        print_tree(node->children[node->n], level + 1);
    }
}

int main() {
    Node* root = NULL;

    insert(&root, "Senhor dos aneis", 10);
    insert(&root, "Poderoso chefao", 5);
    insert(&root, "Pitch Perfect", 15);
    insert(&root, "Barbie", 8);
    insert(&root, "Senhor estagiario", 8);
    insert(&root, "Little women",12);

    printf("B-tree contents:\n");
    print_tree(root, 0);

    char* prefix = "Senhor dos aneis";
    printf("Words with prefix '%s':\n", prefix);
    searchPrefix(root, prefix);
    printf("%d",search(root,prefix));
  
    remove_from_tree(root,prefix);
    print_tree(root, 0);
    printf("Words with prefix '%s':\n", prefix);
  
    searchPrefix(root, prefix);
    return 0;
}

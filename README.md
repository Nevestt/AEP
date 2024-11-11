# AEP

//CODIGO EM C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h> // Inclui biblioteca para suportar setlocale

#define MAX_USUARIOS 100
#define TAM_MAX 50

typedef struct {
    char nome[TAM_MAX];
    char senha[TAM_MAX];
} Usuario;

// Função de Criptografia Simples (XOR)
void criptografar(char *texto) {
    for (int i = 0; i < strlen(texto); i++) {
        texto[i] ^= 42;  // XOR com uma chave simples
    }
}

void descriptografar(char *texto) {
    criptografar(texto);  // O XOR com a mesma chave pode ser usado para criptografar e descriptografar
}

// Função para Adicionar Usuário
void adicionar_usuario() {
    Usuario usuario;
    FILE *arquivo = fopen("usuarios.txt", "a");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo.\n");
        return;
    }

    printf("\nDigite o nome do usuário: ");
    scanf("%s", usuario.nome);

    printf("Digite a senha: ");
    scanf("%s", usuario.senha);
    criptografar(usuario.senha);

    fprintf(arquivo, "%s:%s\n", usuario.nome, usuario.senha);
    fclose(arquivo);
    printf("Usuário adicionado com sucesso!\n\n");
}

// Função para Listar Usuários
void listar_usuarios(int exibir_criptografado) {
    Usuario usuario;
    FILE *arquivo = fopen("usuarios.txt", "r");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo.\n\n");
        return;
    }

    printf("\nUsuários cadastrados:\n");
    while (fscanf(arquivo, "%[^:]:%[^\n]\n", usuario.nome, usuario.senha) != EOF) {
        if (!exibir_criptografado) {
            descriptografar(usuario.senha);
        }
        printf("Nome: %s, Senha: %s\n", usuario.nome, usuario.senha);
        if (!exibir_criptografado) {
            criptografar(usuario.senha);  // Reverte a criptografia para não alterar o arquivo
        }
    }
    printf("\n");
    fclose(arquivo);
}

// Função para Alterar Usuário
void alterar_usuario() {
    Usuario usuarios[MAX_USUARIOS];
    int total = 0;
    char nome[TAM_MAX];
    int encontrado = 0;

    FILE *arquivo = fopen("usuarios.txt", "r");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo.\n");
        return;
    }

    // Carregar todos os usuários no array
    while (fscanf(arquivo, "%[^:]:%[^\n]\n", usuarios[total].nome, usuarios[total].senha) != EOF) {
        total++;
    }
    fclose(arquivo);

    printf("\nDigite o nome do usuário a ser alterado: ");
    scanf("%s", nome);

    for (int i = 0; i < total; i++) {
        if (strcmp(usuarios[i].nome, nome) == 0) {
            encontrado = 1;
            printf("Digite a nova senha: ");
            scanf("%s", usuarios[i].senha);
            criptografar(usuarios[i].senha);
            break;
        }
    }

    if (!encontrado) {
        printf("Usuário não encontrado.\n\n");
        return;
    }

    // Reescrever o arquivo com a atualização
    arquivo = fopen("usuarios.txt", "w");
    for (int i = 0; i < total; i++) {
        fprintf(arquivo, "%s:%s\n", usuarios[i].nome, usuarios[i].senha);
    }
    fclose(arquivo);
    printf("Usuário alterado com sucesso!\n\n");
}

// Função para Excluir Usuário
void excluir_usuario() {
    Usuario usuarios[MAX_USUARIOS];
    int total = 0;
    char nome[TAM_MAX];
    int encontrado = 0;

    FILE *arquivo = fopen("usuarios.txt", "r");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo.\n");
        return;
    }

    // Carregar todos os usuários no array
    while (fscanf(arquivo, "%[^:]:%[^\n]\n", usuarios[total].nome, usuarios[total].senha) != EOF) {
        total++;
    }
    fclose(arquivo);

    printf("\nDigite o nome do usuário a ser excluído: ");
    scanf("%s", nome);

    // Procurar e remover o usuário
    arquivo = fopen("usuarios.txt", "w");
    for (int i = 0; i < total; i++) {
        if (strcmp(usuarios[i].nome, nome) == 0) {
            encontrado = 1;
            continue;  // Ignora o usuário a ser excluído
        }
        fprintf(arquivo, "%s:%s\n", usuarios[i].nome, usuarios[i].senha);
    }
    fclose(arquivo);

    if (encontrado) {
        printf("Usuário excluído com sucesso!\n\n");
    } else {
        printf("Usuário não encontrado.\n\n");
    }
}

// Função Principal com Menu
int main() {
    setlocale(LC_ALL, "Portuguese");  // Define a localidade para Português do Brasil UTF-8
    int opcao;

    do {
        printf("1. Adicionar Usuário\n");
        printf("2. Alterar Usuário\n");
        printf("3. Excluir Usuário\n");
        printf("4. Listar Usuários com senha descriptografada\n");
        printf("5. Listar Usuários com senha criptografada\n");
        printf("6. Sair\n\n");
        printf("Escolha uma opção: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1: 
                adicionar_usuario(); 
                break;
            case 2: 
                alterar_usuario(); 
                break;
            case 3: 
                excluir_usuario(); 
                break;
            case 4: 
                listar_usuarios(0); // Exibir descriptografada
                break;
            case 5: 
                listar_usuarios(1); // Exibir criptografada
                break;
            case 6: 
                printf("Saindo...\n\n"); 
                break;
            default: 
                printf("Opção inválida.\n\n");
        }
    } while (opcao != 6);

    return 0;
}

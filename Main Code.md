#include <stdio.h>		//PROJETO DE ED - MOBILEED
#include <stdlib.h>
#include <locale.h>
#include <windows.h>
#define A 50

typedef struct {
	char app_name[15];
	int app_size;
	int app_id;
} t_app;

int load_app_store(t_app app_store[]);//Função responsável pela leitura e carregamento do arquivo store.
void read_app_store(t_app app_store[], int app_quant);//Função imcubida de leitura do vetor store.
void star_desk(t_app app_store[]);
void write_arq(t_app app_store[], int app_quant);
void menu_mobile(t_app app_store[], int app_quant);
void ordenar(t_app apps_instalados[], t_app app);
int app_verificador(int id, t_app apps_instalados[], int quant_instalados);
int app_out(t_app app_instalados[], int id_out);
int menu_inicial(int naveg);
void mostrar_app_store(t_app app_store[]);
void executar_app(t_app apps_instalados[], t_app apps_run[], int id);
void gotoxy(int x, int y);
void menu_inicial_case1();
void menu_instalar_noapp();
void menu_instalar();
void menu_intalados_excluir();

int main() {
	setlocale(LC_ALL, "portuguese");
	t_app app_store[A];
	int app_quant;
	app_quant = load_app_store(app_store);
	menu_mobile(app_store, app_quant);
	return 0;
}

int load_app_store(t_app app_store[]) {
	FILE * store;
	int app_quant = 0;
	store = fopen("Store.txt", "r");
	if(store == NULL) {
		fclose(store);
		store = fopen("Store.txt", "w");
	} else {
		while(fscanf(store, "Id: %d\nNome: %s\nTamanho: %d\n", & app_store[app_quant].app_id, app_store[app_quant].app_name, & app_store[app_quant].app_size) != EOF) {
			app_quant++;
		}
	}
	fclose(store);
	return app_quant;
}


void menu_mobile(t_app app_store[], int app_quant) {//Principal função da aplicação,controle de fluxo
	int naveg, navegador_app, quant_instalados = 0, i, id_out, var_case2_menu = 2, naveg_run, run, run_count = 0, case1;
	t_app apps_instalados[20], apps_run[20];
	while(naveg != 0) {
		system("cls");
		naveg = menu_inicial(naveg);
		switch(naveg) {
		case 1:
			system("cls");
			menu_inicial_case1();
			read_app_store(app_store, app_quant);
			do {
				scanf("%d", & case1);
				fflush(stdin);
				if(case1 != 0)
					printf("Valor não reconhecido.\n");
			} while(case1 != 0);
			break;
		case 2:
				if(quant_instalados == 0) {
					system("cls");
					menu_instalar_noapp();
				} else {
					system("cls");
					menu_instalar();
					read_app_store(apps_instalados, quant_instalados);
				}
				do {
					scanf("%d", & var_case2_menu);
					fflush(stdin);
					if(var_case2_menu < 0 || var_case2_menu > 2)
						printf("Valor não reconhecido.\n");
				} while(var_case2_menu < 0 || var_case2_menu > 2);

				if(var_case2_menu == 0) {
					break;
				}
				switch(var_case2_menu) {
				case 1:
					system("cls");
					read_app_store(app_store, app_quant);
					printf("Digite o id do app que vc deseja instalar: ");
					scanf("%d", & navegador_app);

					for(i = 0; i < app_quant; i++) {
						if(navegador_app == app_store[i].app_id) {
							if((app_verificador(navegador_app, apps_instalados, quant_instalados)) == 0) {
								ordenar(apps_instalados, app_store[i]);
								quant_instalados++;
								read_app_store(apps_instalados, quant_instalados);
							} else
								printf("App já instalado\n");
						}
					}
					break;
				case 2:
					if(quant_instalados == 0) {
						break;
						
					} else {
						system("cls");
						menu_intalados_excluir();
						read_app_store(apps_instalados, quant_instalados);
						scanf("%d", & id_out);
						if(app_out(apps_instalados, id_out) == 1) {
							quant_instalados--;
						}
					}
					break;
				}
			break;
		case 3:
			if(quant_instalados == 0) {
				printf("Nenhum app instalado impossível carregar\n");
				system("pause");
				break;
			}
			printf("1 - Abrir um app.\n");
			printf("2 - Fechar um app.\n");
			printf("3 - Em execução\n");
			scanf("%d", & naveg_run);
			switch(naveg_run) {
			case 1:
				read_app_store(apps_instalados, quant_instalados);
				printf("Digite o id do app a ser executado: ");
				scanf("%d", & run);
				if((app_verificador(run, apps_instalados, quant_instalados) == 1) && (app_verificador(run, apps_run, run_count) == 0)) {
					executar_app(apps_instalados, apps_run, run);
					run_count++;
				} else {
					printf("App não está instalado ou já está e em execução\n");
					system("pause");
				}
				break;
			case 2:
				if(run_count == 0) {
					printf("Nenhum app em execução.\n");
					system("pause");
					break;
				}
				read_app_store(apps_run, run_count);
				printf("Digite o id do app a ser parado. ");
				scanf("%d", & run);
				if(app_out(apps_run, run) == 1) {
					run_count--;
				} else {
					printf("O app não está em execução.\n");
					system("pause");
					break;
				}
				break;
			case 3:
				read_app_store(apps_run, run_count);
				system("pause");
				break;
			}
			break;

		}
	}
}



void ordenar(t_app apps_instalados[], t_app app) {
	int i, flag;
	for(i = 0; i < 20; i++) {
		if(app.app_size < apps_instalados[i].app_size ) {
			flag = i;
			break;
		}
	}
	for(i = 18; i >= flag; i--) {
		apps_instalados[i + 1] = apps_instalados[i];
	}
	apps_instalados[flag] = app;
}

int app_verificador(int id, t_app apps_instalados[], int quant_instalados) {
	int i;
	for(i = 0; i < quant_instalados; i++) {
		if(id == apps_instalados[i].app_id) {
			return 1;
		}
	}
	return 0;
}

int app_out(t_app app_instalados[], int id_out) {
	int i, j;
	for(j = 0; j < 20; j++) {
		if(app_instalados[j].app_id == id_out) {
			for(i = j; i <= 19; i++) {
				app_instalados[i] = app_instalados[i + 1];
			}
			return 1;
		}
	}
	return 0;
}

void executar_app(t_app apps_instalados[], t_app apps_run[], int id) {
	int i;
	for(i = 0; i < 20; i++) {
		if(apps_instalados[i].app_id == id) {
			ordenar(apps_run, apps_instalados[i]);
			return;
		}
	}
}


int menu_inicial(int naveg) {
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  1.App Store   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  2.Instalados  |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  3.App Run     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |     0.Sair     |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
	scanf("%d", & naveg);
	return naveg;
}

void read_app_store(t_app app_store[], int app_quant) {
	int i, coluna = 6, linha = 1;
	for(i = 0; i < app_quant; i++) {
		gotoxy(coluna, linha + i);
		printf("%d", app_store[i].app_id);
		gotoxy(coluna + 3, linha + i);
		printf("%s", app_store[i].app_name);
		gotoxy(coluna + 15, linha + i);
		printf("%dmb", app_store[i].app_size);
	}
	printf("\n\n\n\n");
}

void gotoxy(int x, int y) {
	COORD coord;
	coord.X = x;
	coord.Y = y;
	SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

void menu_inicial_case1() {
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                 Apps Instalados         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.Voltar     |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_instalar_noapp() {
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.Instalar   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|       NENHUM APP INSTALADO AINDA                        |   2.Excluir    |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.Voltar     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_instalar() {
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.Instalar   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   2.Excluir    |\n");
	printf("|                                  INSTALADOS             |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.Voltar     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_intalados_excluir() {
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.Instalar   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   2.Excluir    |\n");
	printf("|                                DIGITE O ID DO APP       |                |\n");
	printf("|                                A SER EXCLUÍDO.          |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.Voltar     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}


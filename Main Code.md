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
void menu_inicial();
void mostrar_app_store(t_app app_store[]);
void executar_app(t_app apps_instalados[], t_app apps_run[], int id);
void gotoxy(int x, int y);
void menu_inicial_case1();
void menu_instalar_noapp();
void menu_instalar();
void menu_intalados_excluir();
void menu_inicial_instalar();
void menu_executar();
void read_run(t_app run[][3], int run_count);
void copy_run(t_app desk[][3], t_app run[], int run_count);

int main() {
	setlocale(LC_ALL, "portuguese");
	t_app app_store[A];
	int app_quant = 0;
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
	int naveg, navegador_app, quant_instalados = 0, i, id_out, var_case2_menu = 2, naveg_run, run, run_count = 0, case1, flag = 2;
	t_app apps_instalados[20], apps_run[20], desktop[3][3];
	while(naveg != 0) {
		system("cls");
		menu_inicial();
		if(quant_instalados == 0) {
			gotoxy(20, 11);
			printf("NENHUM APP INSTALADO");
		} else {
			copy_run(desktop, apps_instalados, quant_instalados);
			read_run(desktop, quant_instalados);
		}
		gotoxy(1, 24);
		scanf("%d", & naveg);
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
			do {
				if(quant_instalados == 0) {
					system("cls");
					menu_instalar_noapp();
				} else {
					system("cls");
					menu_instalar();
					read_app_store(apps_instalados, quant_instalados);
				}
				do {
					gotoxy(1, 24);
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
					do {
						system("cls");
						menu_inicial_instalar();
						read_app_store(app_store, app_quant);
						do {
							scanf("%d", & navegador_app);
						} while(navegador_app < 0 || navegador_app >= 20);
						for(i = 0; i < app_quant; i++) {
							if(navegador_app == app_store[i].app_id) {
								if((app_verificador(navegador_app, apps_instalados, quant_instalados)) == 0) {
									ordenar(apps_instalados, app_store[i]);
									quant_instalados++;
									read_app_store(apps_instalados, quant_instalados);
									flag = 0;
								} else {
									gotoxy(34, 11);
									printf("APP JÁ INSTALADO DESEJA\n");
									gotoxy(34, 12);
									printf("TENTAR NOVAMENTE ?\n");
									gotoxy(34, 13);
									printf("0 - NÃO.\n");
									gotoxy(34, 14);
									printf("1 - SIM.\n");
									gotoxy(34, 15);
									scanf("%d", & flag);
								}
							}
						}
					} while(flag == 1);
					break;
				case 2:
					do {
						if(quant_instalados == 0) {
							break;

						} else {
							system("cls");
							menu_intalados_excluir();
							read_app_store(apps_instalados, quant_instalados);
							do {
								gotoxy(1, 24);
								scanf("%d", & id_out);
								if(id_out <= 0 || id_out > 20)
									gotoxy(33, 16);
								printf("Valor inválido.");
							} while(id_out <= 0 || id_out > 20);

							if(app_out(apps_instalados, id_out) == 1) {
								quant_instalados--;
								flag = 0;
							} else {
								gotoxy(33, 11);
								printf("APP NÃO LOCALIZADO");
								gotoxy(33, 12);
								printf("TENTAR NOVAMENTE ?");
								gotoxy(33, 13);
								printf("1 - SIM");
								gotoxy(33, 14);
								printf("2 - NÂO");
								gotoxy(33, 15);
								scanf("%d", & flag);
							}
						}
					} while(flag == 1);
					break;
				}
			} while(var_case2_menu != 0);
			break;
		case 3:
			do {
				system("cls");
				menu_executar();
				copy_run(desktop, apps_instalados, quant_instalados);
				if(run_count == 0) {
					gotoxy(20, 11);
					printf("NENHUM APP EM EXECUÇÃO");
				} else {
					gotoxy(35, 10);
					printf("EXECUÇÃO");
					read_app_store(apps_run, run_count);
				}
				gotoxy(61, 4);
				printf("1.ABRIR APP\n");
				gotoxy(61, 10);
				printf("2.FECHAR APP\n");
				gotoxy(61, 16);
				printf("0.VOLTAR");
				gotoxy(1, 24);
				scanf("%d", & naveg_run);
				switch(naveg_run) {
				case 1:
					if(quant_instalados == 0) {
						gotoxy(20, 11);
						printf("NENHUM  APP  INSTALADO");
						gotoxy(10, 21);
						system("pause");
						break;
					}
					system("cls");
					menu_executar();
					read_app_store(apps_instalados, quant_instalados);
					gotoxy(33, 11);
					printf("DIGITE O ID DO APP");
					gotoxy(33, 12);
					printf("A SER EXECUTADO: ");
					gotoxy(1, 24);
					scanf("%d", & run);
					if((app_verificador(run, apps_instalados, quant_instalados) == 1) && (app_verificador(run, apps_run, run_count) == 0)) {
						executar_app(apps_instalados, apps_run, run);
						run_count++;
					} else {
						system("cls");
						menu_executar();
						gotoxy(5, 10);
						printf("APP NÃO INSTALADO OU APP EM EXECUÇÃO");
						gotoxy(5, 21);
						system("pause");
					}
					break;
				case 2:
					if(run_count == 0) {
						system("cls");
						menu_executar();
						gotoxy(5, 10);
						printf("NENHUM APP EM EXECUÇÃO\n");
						gotoxy(5, 21);
						system("pause");
						break;
					}
					system("cls");
					menu_executar();
					read_app_store(apps_instalados, quant_instalados);
					gotoxy(33, 11);
					printf("DIGITE O ID DO APP");
					gotoxy(33, 12);
					printf("A SER PARADO: ");
					gotoxy(1, 24);
					scanf("%d", & run);
					if(app_out(apps_run, run) == 1) {
						run_count--;
					} else {
						system("cls");
						menu_executar();
						gotoxy(5, 10);						
						printf("O APP NÃO ESTA EM EXECUÇÃO\n");
						gotoxy(5, 21);
						system("pause");
						break;
					}
					break;
				}
			} while(naveg_run != 0);
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
	return -1;
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


void menu_inicial() {
	printf("\n");
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  1.APP STORE   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  2.INSTALADOS  |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  3.EXECUÇÃO    |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |  0.DESLIGAR    |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");

}

void read_app_store(t_app app_store[], int app_quant) {
	int i, coluna = 6, linha = 2;
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

void read_run(t_app desktop[][3], int run_count) {
	int i, coluna, linha, j, aux = 0;
	for(j = 0, linha = 5; j < 3; j++, linha += 7) {
		for(i = 0, coluna = 2; i < 3; i++, coluna += 20) {
			gotoxy(coluna, linha);
			printf("%d.%s", desktop[j][i].app_id, desktop[j][i].app_name);
			aux++;
			if(run_count == aux) {
				return;
			}
		}
	}
}

void copy_run(t_app desk[][3], t_app run[], int run_count) {
	int i, j, aux = 0;
	for(i = 0; i < 3; i++) {
		for(j = 0; j < 3; j++) {
			desk[i][j] = run[aux];
			aux++;
		}
	}
}


void menu_inicial_case1() {
	printf("\n");
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
	printf("|                                 APPS INSTALADOS         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.VOLTAR     |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_instalar_noapp() {
	printf("\n");
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.INSTALAR   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|       NENHUM APP INSTALADO AINDA                        |   2.EXCLUIR    |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.VOLTAR     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_instalar() {
	printf("\n");
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.INSTALAR   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   2.EXCLUIR    |\n");
	printf("|                                  INSTALADOS             |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.VOLTAR     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_intalados_excluir() {
	printf("\n");
	printf(" ==========================================================================\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   1.INSTALAR   |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   2.EXCLUIR    |\n");
	printf("|                                DIGITE O ID DO APP       |                |\n");
	printf("|                                A SER EXCLUÍDO.          |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |   0.VOLTAR     |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_inicial_instalar() {
	printf("\n");
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
	printf("|                                 DIGITE O ID DO APP      |                |\n");
	printf("|                                 A SER INSTALADO.        |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

void menu_executar() {
	printf("\n");
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
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |================|\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf("|                                                         |                |\n");
	printf(" ==========================================================================\n");
}

# Projeto-de-ED-I
Esse projeto e sobre o a matéria de programação estrutudada do segundo período.
#include <stdio.h>		//PROJETO DE ED - MOBILEED
#include <stdlib.h>
#include <locale.h>
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
void app_out(t_app app_instalados[], int id_out);

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

void read_app_store(t_app app_store[], int app_quant) {
	int i;
	printf("ID====Nome==============Tamanho====\n");
	for(i = 0; i < app_quant; i++) {
		if(i < 9) {
			printf("%d     %s:             %dmb\n\n", app_store[i].app_id, app_store[i].app_name, app_store[i].app_size);
		} else {
			printf("%d    %s:             %dmb\n\n", app_store[i].app_id, app_store[i].app_name, app_store[i].app_size);
		}
	}
}

void menu_mobile(t_app app_store[], int app_quant) {
	int naveg, navegador_app, quant_instalados = 0, i, id_out;
	t_app apps_instalados[20];
	while(naveg != 0) {
		system("cls");
		printf("1.App Store\n");
		printf("2.Apps Instalados\n");
		printf("3.Apps Run\n");
		printf("0.Desligar\n");
		scanf("%d", & naveg);
		fflush(stdin);
		switch(naveg) {
		case 1:
			system("cls");
			read_app_store(app_store, app_quant);
			printf("\n\n");
			system("pause");
			break;
		case 2:
			printf("Nenhum App instalado ainda, deseja instalar algum ?\n");
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
			if(quant_instalados > 5) {
				printf("Digite o id do app a ser desinstalado: ");
				scanf("%d", & id_out);
				app_out(apps_instalados, id_out);
				quant_instalados--;
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

void app_out(t_app app_instalados[], int id_out) {
	int i;
	for(i = id_out; i <= 19; i++) {
		app_instalados[i] = app_instalados[i + 1];
	}
}

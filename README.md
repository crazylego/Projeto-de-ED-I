# Projeto-de-ED-I
Esse projeto e sobre o a matéria de programação estrutudada do segundo período.

#include <stdio.h>
#define n 5

int main() {
	int i, vet[n];

	for(i = 0; i < n; i++) {
		scanf("%d", & vet[i]);
	}

	for(i = n - 2; i >= 1; i--) {
		vet[i+1] = vet[i];
	}

	for(i = 0; i < n; i++) {
		printf("%d ", vet[i]);
	}

	return 0;
}

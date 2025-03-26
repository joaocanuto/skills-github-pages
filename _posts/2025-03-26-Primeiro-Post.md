---
title: "Primeiro Post"
date: 2025-03-26
---

## Subset Sum
I ) Zero subsets is a valid solution, so the ```dp[0] = 1```
II ) Recursive Form : 
```cpp
bool isSubsetSum(int set[], int n, int sum){
	
	if(sum == 0) return true;
	
	if(n == 0) return false;  
	
	if(set[n-1] > sum){
		return isSubsetSum(set,n-1,sum);		
	}
	
	
	return 
		isSubsetSum(set,n-1,sum) 
		|| 
		isSubsetSum(set, n-1,sum - set[n-1]);
}
```
Complexity: O(2ˆn)
### The DP Approach 
* Use sum as a index of a matrix like: 
  ```dp[idx][sum]```
* First, its important that sum is greater than zero.
* We look for the :
    ```dp[idx][sum] = dp[idx - 1 ]...```
    So the point is the previous line of the 2D Dp is already filled. 
```cpp
int countSubsets(int set[], int n, int sum){
	int dp[n+1][sum+1];
	for(int i = 0; i < n; i++){
		dp[i][0] = 1; 
	}
	for(int j = 1; j <= sum; j++){
		dp[0][j] = 0;
	}

	for(int i = 1; i <= n; i++){
		for(int j = 1; j <= sum; j++){
			if(j < arr[i-1])
				dp[i][j] = dp[i-1][j];
			else 
				dp[i][j] =
					dp[i-1][j] + 
					dp[i][j - arr[i-1]];
		}
	}
	return dp[n][sum];
}
```
### Count Ways to Reach Nth Stair - O(n)
```cpp
// the main ideia is a iterative DP, going from 0 to N. Similary the ideia from fibbonacci in linear time.

int dp(int n){
	memo[1] = 1;
	memo[2] = 2;

	for(int i = 3; i <= n; i++){
		memo[i] = memo[i-1] + memo[i-2];
	}
	return memo[n];
}
```
### Minimum jumps to reach end 
Nesse problema, nosso objetivo é sair de 0 até o N, dando saltos de tamanho máximo ```arr[i]```. Qual o menor numero de saltos que devemos dar ? 
`Exemplo: arr[] = {1,3,9,8,2,6,2,7}`
`Solution: 1 -> 3 -> 8 -> 7 (3 Jumps)`
```cpp
// The approach of this problem is to look for the end, and start from there, looking for each jump that reach here. 

int minJumps(int a[], int n, int i){
	if(i >= n-1) return 0;
	int& mem = cache[i];
	if(mem != -1) return mem;

	int res = INF;
	for(int j = 1; j <= a[i]; j++){
		res = min(res, 1 + minJumps(a,n,i+j));
	}

	return mem = res;
}
```
Complexity : `O(n*max(a[i]))`
### House Thief Problem
Temos N casas disponíveis e em cada casa temos um valor. Queremos máximos o tanto de valor que conseguimos pegar as casas de modo que não podemos pegar valores de casas adjacentes. 
```cpp
// The approach is imagina a 2D array that save a state, true or false, that element 
int maxSteal(int values[], int n, int i, bool canSteal){
	if(i == n) return 0;
	int& mem = cache[i][canSteal];

	if(mem != -1) return mem;

	int in = 0, ex = 0;
	if(canSteal){
		in = values[i] + maxSteal(values, n, i + 1, false);
	}
	ex = maxSteal(values, n, i + 1, true);

	return mem = max(in,ex);
}
```
### Kanade's Algorithm : 
Maximum subarray sum
```cpp
int maximumSubarraySum(vector<int>& arr){
	int n = arr.size();
	vector<int> dp(n);
	dp[0] = arr[0];
	for(int i = 0; i < n; i++){
		dp[i] = max(arr[i] + dp[i-1], arr[i]);
	}
	return *max_element(dp.begin(), dp.end());
}
```
### Allocate Minimum Pages:
Given a number of pages in **N** different books and **M** students. The books are arranged in ascending order of the number of pages. Every student is assigned to read some consecutive books. The task is to assign books in such a way that the maximum number of pages assigned to a student is minimum.
```c++
arr[] = {10,20,30,40}
k = 2
O/P = 60

arr[] = {10,20,30}
k = 1 
O/P = 60

arr[] = {10,5,30,1,2,5,10,10}
k = 3
O/P = 30

CODE:

int minPages(int arr[], int n, int k){
	if(k==1) return sum(arr,0, n-1);
	if(n==1) return arr[0];
	int res = INF;
	for(int i = 1; i < n; i++){
		res = min(
			res, 
			max(
				minPages(arr, i, k-1), 
				sum(arr,i,n-1)
			)
		);
	}
	return res; 
}

int sum(int arr[], int b, int e){ // can be a prefix sum
	int s=0;
	for(int i = b; i <= e; i++){
		s+= arr[i];
	}
	return s;
}

/*
This approach is:
I will try to give "i" books for a students, and i will try to give the rest books for the m - 1 students. But this approach is slow.
*/
```

The better approach is using binary search, for guessing the number in a range that will be the answer for a students. So, the ideia is:
Guess a value MID, that ranges in  (min(pages), max(pages)), and check if this is a valid amount for each student, if is valid, than a i will check with a another guess.
- Initialise the **start** to **maximum(pages[])** and **end =** sum of **pages[]**,
- Do while start <= end
    - Calculate the mid and check if **mid** number of pages can assign any student by satisfying the given condition such that all students will get at least one book. Follow the steps to check for validity.
        - Initialise the **studentsRequired = 1** and **curr_sum = 0** for sum of consecutive pages of book
        - Iterate over all books or say pages[]
            - Add the pages to curr_sum and check **curr_sum > curr_min** then increment the count of **studentRequired** by 1.
            - Check if the studentRequired > M, return false.
        - Return true.
    - If mid is valid then, update the **result** and move the **end = mid – 1**
    - Otherwise, move the **start = mid + 1**
- Finally, return the **result**
```c++
#include <bits/stdc++.h>
using namespace std;

bool isPossible(int arr[], int n, int m, int curr_min){
	int studentsRequired = 1;
	int curr_sum = 0;
	for (int i = 0; i < n; i++) {
		if (arr[i] > curr_min)
			return false;

		if (curr_sum + arr[i] > curr_min) {
			studentsRequired++;
			curr_sum = arr[i];

			if (studentsRequired > m)
				return false;
		}
		else
			curr_sum += arr[i];
	}
	return true;
}
int findPages(int arr[], int n, int m){
	long long sum = 0;

	if (n < m)
		return -1;
	int mx = INT_MIN;

	for (int i = 0; i < n; i++) {
		sum += arr[i];
		mx = max(mx, arr[i]);
	}
	int start = mx, end = sum;
	int result = INT_MAX;

	while (start <= end) {
		int mid = (start + end) / 2;
		if (isPossible(arr, n, m, mid)) {
			result = mid;
			end = mid - 1;
		}

		else
			start = mid + 1;
	}

	return result;
}

int main(){
	int arr[] = { 12, 34, 67, 90 };
	int n = sizeof arr / sizeof arr[0];
	int m = 2;

	cout << "Minimum number of pages = "
		<< findPages(arr, n, m) << endl;
	return 0;
}
```
#### DP Approach:
```c++
dp[i][j] = minimum pages for i people and j books

int minPages(int arr[], int n, int k){
	int dp[k+1][n+1];
	for(int i = 1; i <= n; i++){
		dp[1][k] = sum(arr, 0, i - 1);
	}
	for(int i=1; i <= k; i++){
		dp[i][1] = arr[0];
	}

	for(int i = 2; i <= k; i++){
		for(int j = 2; j<= n; j++){
			int res = INF;
			for(int p = 1; p < j; p++){
				res = min(res,
					max(
						dp[i-1][p],
						sum(arr,p,j-1)
					)
				);
			}
			dp[i][j] = res;
		}
	}
	return dp[k][n];
}

O(n*n*n*k);

With prefix sum to the sum
O(n*n*k); // much time ainda!
```
### LCS:
```c++
int pd[1000][1000];

int LCS(string a, string b){
    for(int i = 1; i <= a.size(); i++){
        for(int j = 1; j <= b.size(); j++){
            if(a[i-1] == b[j-1]) pd[i][j] = pd[i-1][j-1] + 1;
            else pd[i][j] = max(
                pd[i-1][j],
                pd[i][j-1]
            );
        }
    }
    return pd[a.size()][b.size()];
}

string LCS_Str(string a, string b){
    int i = a.size(), j = b.size();
    string ans;
    while( i > 0 & j > 0 ){
        if(a[i-1] == b[j-1]){
            ans.push_back(a[i-1]);
            i--;
            j--;
        } else if(pd[i-1][j] > pd[i][j-1]){
            i--;
        } else {
            j--;
        }
    }
    reverse(ans.begin(), ans.end());
    return ans; 
}
```
### LIS:
```c++
#include <bits/stdc++.h>
using namespace std;

void lis(vector<int> &arr)
{
	vector<int> pilha;
	int pai[1000], pos[1000];
	for(int i = 0; i < arr.size(); i++)
	{
		int p = int(upper_bound(pilha.begin(),
			 pilha.end(), arr[i]) - pilha.begin());
		if(p == pilha.size())
			pilha.push_back(arr[i]);
		else
			pilha[p] = arr[i];
		pos[p] = i;
		if(!p)
			pai[i] = -1;
		else
			pai[i] = pos[p - 1];
	}
	vector<int> L;
	int aux = pos[pilha.size() - 1];
	cout << pilha.size() << '\n';
	while(aux != -1)
	{
		L.push_back(arr[aux]);
		aux = pai[aux];
	}
	reverse(L.begin(), L.end());
	for(const int &w : L)
		cout << w << ' ';
	cout << '\n';
}

int main()
{
	int n;
	cin >> n;
	vector<int> arr(n);
	for(int &w : arr) 
		cin >> w;
	lis(arr);
	
	return 0;
}
```
Vamos analisar o que esse código faz e por que ele consegue **imprimir** (e não só calcular o tamanho) da **Longest Increasing Subsequence (LIS)** em tempo O(nlog⁡n)O(n \log n).

---

## Visão geral

A função `lis` recebe um vetor `arr` e faz três coisas principais:

1. **Encontra o tamanho da LIS** usando o clássico método do **“vetor de tails” + binary search”**.
2. **Armazena informações** de como chegou em cada valor de “tail” para conseguir reconstruir a subsequência depois.
3. **Reconstrói e imprime** a subsequência de fato (não apenas o seu tamanho).

### Passo 1: Vetor `pilha` e busca binária (`upper_bound`)

```cpp
vector<int> pilha;
int pai[1000], pos[1000];

for(int i = 0; i < arr.size(); i++)
{
    // Acha a posição p em "pilha" usando upper_bound
    int p = int(upper_bound(pilha.begin(), pilha.end(), arr[i]) - pilha.begin());

    if(p == (int)pilha.size())
        pilha.push_back(arr[i]);
    else
        pilha[p] = arr[i];

    pos[p] = i;

    if(!p)
        pai[i] = -1;
    else
        pai[i] = pos[p - 1];
}
```

- `pilha` (chamada assim no código) é aquilo que, em muitas explicações de LIS, chamamos de **array de tails**.
    
    - `pilha[k]` guardará **o menor valor possível** que pode terminar uma subsequência crescente de tamanho (k+1).
- A chamada:
    
    ```cpp
    upper_bound(pilha.begin(), pilha.end(), arr[i])
    ```
    
    encontra o **primeiro** lugar em `pilha` onde o valor **maior** do que `arr[i]` aparece. Isso nos indica o tamanho da subsequência que podemos **estender** (ou melhorar) com `arr[i]`.
    
    - Se `p == pilha.size()`, quer dizer que `arr[i]` é maior que todos em `pilha`, então podemos **criar** uma subsequência de tamanho maior (ou seja, a LIS atual aumentou de tamanho).
    - Caso contrário, substituímos `pilha[p]` por `arr[i]`, “melhorando” o valor final de uma subsequência de tamanho (p+1) (menor valor de término = mais chances de conseguir subsequência maior no futuro).

---

### Passo 2: Guardando informações para reconstruir a subsequência

Temos dois arrays extras:

- `pos[p]`: diz qual é o **índice do arr** que está terminando a subsequência de tamanho (p+1).  
    Exemplo: se `p = 2`, então `pos[2]` é o índice do último elemento (em `arr`) de uma subsequência de tamanho 3.
    
- `pai[i]`: guarda o **índice** do elemento anterior a `arr[i]` na subsequência crescente.
    
    - Se `p == 0`, não há antecessor (pois é a subsequência inicial), logo `pai[i] = -1`.
    - Senão, `pai[i] = pos[p - 1]`, indicando que o antecessor de `arr[i]` na subsequência está no índice que finalizava a subsequência de tamanho `p`.

Essas variáveis são atualizadas toda vez que inserimos/substituímos um valor em `pilha[p]`.

---

### Passo 3: Reconstruindo a subsequência

```cpp
vector<int> L;
int aux = pos[pilha.size() - 1]; // índice do último elemento da maior subsequência
cout << pilha.size() << '\n';   // imprime o tamanho da LIS

while(aux != -1) {
    L.push_back(arr[aux]);
    aux = pai[aux];
}
reverse(L.begin(), L.end());

// imprime a sequência
for(const int &w : L)
    cout << w << ' ';
cout << '\n';
```

- O tamanho da LIS está em `pilha.size()`.
- Para reconstruir de fato a subsequência, iniciamos em `aux = pos[pilha.size() - 1]`, ou seja, o índice do **último elemento** da subsequência de tamanho máximo.
- A partir desse índice, usamos `pai[aux]` para descobrir **quem era o antecessor** no LIS, e assim sucessivamente, até `pai[aux] = -1`.
- Isso vai dar a subsequência em **ordem inversa**, por isso usamos `reverse` no final.
- Finalmente, imprimimos a subsequência reconstruída.

---

## Resumo

1. **`pilha`** faz o papel de **array de “tails”** no algoritmo clássico de LIS O(nlog⁡n)O(n \log n).
2. **`pos`** e **`pai`** são arrays auxiliares que nos permitem rastrear **quem é o antecessor** de cada elemento usado, para reconstruir a subsequência.
3. Ao final, **`pilha.size()`** é o **tamanho** da LIS, e percorremos a cadeia de antecessores a partir do último elemento para efetivamente **imprimir** a LIS.

Desse modo, você obtém tanto o **tamanho** quanto a **subsequência crescente** de forma eficiente.
`1 2 2 2 3 3 4 4 4 4` 
`  0 1 0 0 0 0 1 1 `   

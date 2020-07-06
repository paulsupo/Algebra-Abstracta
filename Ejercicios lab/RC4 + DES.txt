#include <iostream>
#include <windows.h>
#include <stdio.h>
#include <fstream>
#include <string>
#include <sstream>// zz to string
#include <NTL/ZZ.h>
#include <vector>
#include <random>
#include <bitset>
using namespace NTL;
using namespace std;
string alfabeto = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";

class RC4{
private:
    ZZ semilla;
    ZZ semillabits;
    ZZ A;
    ZZ B;
public:
    string alfabeto = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    ZZ bits = conv<ZZ>(100);//cantidad de bits
    vector<string> K;
    vector<string> S;
    DWORD nicks=0*459849;
    RC4(ZZ,ZZ);
    ZZ usobits();
    ZZ convertirbit(string);
    ZZ generadorS();
    ZZ potenciar(ZZ,ZZ);
    string principal();
    ZZ stringtozz(string);
    void iteraciones(vector<string>&);
    string zztostring(ZZ);
    string stringtoBinario(string);
    ZZ modulozz(ZZ,ZZ);
    int zztoint(ZZ);
    vector<string> secuenciacifrante(vector<string>&);
};

RC4::RC4(ZZ _A,ZZ _B){
    semilla=generadorS();
    semillabits=usobits();
    A=_A;
    B=_B;
}

ZZ RC4::stringtozz(string str){
    ZZ z(NTL::INIT_VAL, str.c_str());
    return z;
}

string RC4::zztostring(ZZ num) {
    stringstream buffer;
    buffer << num;
    return buffer.str();
}

ZZ RC4::potenciar(ZZ base, ZZ elevar) {
	ZZ total, i;
	total = 1; i = 0;
	for (i; i < elevar; i++)
		total *= base;

	return total;
}

ZZ RC4::convertirbit(string mensaje) {
	int len = mensaje.size() - 1, algo = len;
	ZZ num, dos;
	num = 0; dos = 2;
	ZZ ii;
	for (int i = 0; i <= algo; i++, len = algo - i) {
		ii = i;
		if (mensaje[len] == '1')
			num += potenciar(dos, ii);
	}
	return num;
}

ZZ RC4::usobits(){
    ZZ x,i,minimo,maximo,valor1,valor2;
    x=1;
    for(i=0;i<bits;i++)
        x=x*2;

    minimo=x/2;
    maximo=x-1;
    ZZ adan;
    adan=semilla;
    bool popa=true,papa=true;
    string nani=zztostring(minimo);
    string te=zztostring(adan);
    valor1=nani.length();
    valor2=te.length();
    if(valor2<valor1){
        ZZ gaa,xddd;
        xddd=valor1-valor2;
        for(int jo=0,gaa=0;gaa<xddd;gaa++,jo++)
            te=te+te[jo];

        adan=stringtozz(te);
    }
    else{
        while(popa==true){
            string tempe=zztostring(adan);
            if(minimo<adan && adan<maximo){
                adan=stringtozz(tempe);
                popa=false;
            }
            else{
                tempe.erase(0,1);
                adan=stringtozz(tempe);
            }
            popa=false;
            i++;
        }
    }
    cout<<"interbalo de bits de "<<x/2<<" a "<<x-1 <<endl;
    return adan;
}

ZZ RC4::generadorS(){
    HWND ventana;
    DWORD pid;
    HANDLE hp;
    char buffer[222];//char usado para almacenar una direccion de memoria no afecta al codigo
    ventana=FindWindow(0,"steam");//aplicacion determinada
    GetWindowThreadProcessId(ventana,&pid);
    hp=OpenProcess(PROCESS_ALL_ACCESS,true,pid);
    ReadProcessMemory(hp,(PBYTE*)nicks,&buffer,sizeof(buffer),0);
    ZZ x = conv<ZZ>(pid);//convierto int a zz
    return x;
}

ZZ RC4::modulozz(ZZ a, ZZ b) {
	if (a >= 0)
		return a - (a / b) * b;
	else
		return a - ((a / b) - 1) * b;
}

int RC4::zztoint(ZZ num) {
	string temp = zztostring(num);
	int numero = stoi(temp);
	return numero;
}

void RC4::iteraciones(vector<string> &K){
    ZZ f,i,Si,Ki,cuatro;
    string ricardomilos;
    string KO;
    cuatro=A;//modulo a ();
    int temp,contador;
    KO=zztostring(semillabits);
    cout<<"KO ="<<KO[7]<<endl;
    string paultemp;
    int paco=KO.length();
    for(i=0;i<A;i++){
        if(contador>=paco)
            contador=0;

        temp=zztoint(i);
        string aea=K[temp];
        paultemp=KO[contador];
        Si=stringtozz(aea);
        Ki=stringtozz(paultemp);
        f=modulozz((f+Si+Ki),cuatro);
        contador=contador+1;
        //variables cambio
        ricardomilos=K[temp];
        K[temp]=K[zztoint(f)];
        K[zztoint(f)]=ricardomilos;
    }
}

string RC4::stringtoBinario(string mensaje) {
    string algo, men;
    int indice = alfabeto.find(mensaje[0]);
    for (int i = 0; i < mensaje.size(); i++) {
        indice = alfabeto.find(mensaje[i]);
        bitset<8> m(indice);
        algo = m.to_string();
        men += algo;
    }
    return men;
}

vector<string> RC4::secuenciacifrante(vector<string> &S){
    ZZ I,F,axe,t,uwu;
    string ricardomilos;
    vector<string>::iterator indice=K.begin();
    for(axe=0;axe<A;axe++){
        I=modulozz(I+1,A);
        F=modulozz(F+stringtozz(S[zztoint(axe+1)]),A);
        //cout<<"I :"<<I<<" F :"<<F<<endl;
        //syap
        ricardomilos=S[zztoint(I)];
        S[zztoint(I)]=S[zztoint(F)];
        S[zztoint(F)]=ricardomilos;
        uwu=stringtozz(S[zztoint(I)])+stringtozz(S[zztoint(F)]);
        t=modulozz(uwu,A);
        string oreimo=stringtoBinario(S[zztoint(t)]);
        K.push_back(zztostring(convertirbit(oreimo)));
    }
    return K;

}

string RC4::principal(){
    cout<<semilla<<" "<<B<<" "<<A<<endl;
    cout<<"semilla reducida a bits :"<<semillabits<<endl;//repitiendo la semilla
    cout<<"a :"<<A<<endl;
    ZZ c,x;
    c=1;
    for(x=1;x<A+c;x++)
        K.push_back(zztostring(x));

    vector<string>::iterator indice=K.begin();
    for (indice; indice != K.end(); indice++) //vector l
       cout << *indice <<",";

    cout<<endl<<"marca de agua"<<endl;
    iteraciones(K);
    string jackElDestripador, stringfinal;
    vector<string>::iterator aw=K.begin();
    for (aw; aw != K.end(); aw++){ //vector l
        jackElDestripador+=(*aw);
        cout << *aw <<",";
    }
    cout<<endl<<"generacion de secuencia cifrante "<<endl;
    vector<string> hayasaka;//final
    hayasaka=secuenciacifrante(K);
    vector<string>::iterator kaguya;
    for(kaguya=hayasaka.begin(); kaguya!=hayasaka.end(); kaguya++){
        stringfinal+=(*kaguya);
        cout<<*kaguya;
  }
    cout<<endl<<"primer paso"<<endl<<jackElDestripador<<endl;
    return stringfinal;
}

///DES/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
ZZ stringtozz(string str){
	ZZ z(NTL::INIT_VAL, str.c_str());
	return z;
}

int moduloint(int a, int b) {
	if (a >= 0)
		return a - (a / b) * b;
	else
		return a - ((a / b) - 1) * b;
}

ZZ potenciar(ZZ base, ZZ elevar) {
	ZZ total, i;
	total = 1; i = 0;
	for (i; i < elevar; i++) {
		total *= base;
	}
	return total;
}

ZZ convertirbit(string mensaje) {
	int len = mensaje.size() - 1, algo = len;
	ZZ num, dos;
	num = 0; dos = 2;
	ZZ ii;
	for (int i = 0; i <= algo; i++, len = algo - i) {
		ii = i;
		if (mensaje[len] == '1')
			num += potenciar(dos, ii);
	}
	return num;
}

string obtenerstring(string mensaje) {
	string algo, men;
	int indice = alfabeto.find(mensaje[0]);
	for (int i = 0; i < mensaje.size(); i++) {
		indice = alfabeto.find(mensaje[i]);
		bitset<4> m(indice);
		algo = m.to_string();
		men += algo;
	}
	return men;
}

string permutacion(string mensaje) {
	string retorna;
	int tabla[] = { 56, 48, 40, 32, 24, 16, 8, 0, 57, 49, 41, 33, 25, 17, 9, 1, 58, 50, 42, 34, 26, 18, 10, 2, 59, 51, 43, 35, 62, 54, 46, 38, 30, 22, 14, 6, 61, 53, 45, 37, 29, 21, 13, 5, 60, 52, 44, 36, 28, 20, 12, 4, 27, 19, 11, 3 };
	int size = sizeof(tabla) / sizeof(tabla[0]);
	for (int i = 0; i < size; i++)
		retorna += mensaje[tabla[i]];
	return retorna;
}

string permutacion2(string mensaje) {
	string retorna;
	int tabla[] = { 13, 16, 10, 23, 0, 4, 2, 27, 14, 5, 20, 9, 22, 18, 11, 3, 25, 7, 15, 6, 26, 19, 12, 1, 40, 51, 30, 36, 46, 54, 29, 39, 50, 44, 32, 47, 43, 48, 38, 55, 33, 52, 45, 41, 49, 35, 28, 31 };
	int size = sizeof(tabla) / sizeof(tabla[0]);
	for (int i = 0; i < size; i++)
		retorna += mensaje[tabla[i]];
	return retorna;
}

string correizquierda(string mensaje, int x) {
	string res = mensaje;
	char al;
	for (int i = 0; i < x; i++) {
		bitset<28> m(res);
		al = res[0];
		m <<= 1;
		res = m.to_string();
		res.pop_back();
		res += al;
	}
	return res;
}

string generarclavek(string c, string d, int x) {
	string c0 = correizquierda(c, x);
	string d0 = correizquierda(d, x);
	string cd0;
	cd0 = cd0 + c0 + d0;
	string k1 = permutacion2(cd0);
	return k1;
}

string sumatodoslosk(string base){
    base=obtenerstring(base);
    base=permutacion(base);
    string c, d, cd, k1, k2, k3, k4, k5, k6, k7, k8, k9, k10, k11, k12, k13, k14, k15, k16, suma;
	for (int i = 0; i < base.size() / 2; i++)
		c += base[i];

	for (int i = 28; i < base.size(); i++)
		d += base[i];

    k1=generarclavek(c,d,1);
   //cout << "K1:  "<< k1 <<endl;

	k2 = generarclavek(c, d, 2);
	//cout << "K2:  "<< k2 <<endl;

	k3 = generarclavek(c, d, 4);
	//cout << "K3:  "<< k3 <<endl;

	k4 = generarclavek(c, d, 6);
	//cout << "K4:  "<< k4 <<endl;

	k5 = generarclavek(c, d, 8);
	//cout << "K5:  "<<k5 << endl;

	k6 = generarclavek(c, d, 10);
	//cout << "K6:  "<<k6 << endl;

	k7 = generarclavek(c, d, 12);
	//cout << "K7:  "<<k7 << endl;

	k8 = generarclavek(c, d, 14);
	//cout << "K8:  "<<k8 << endl;

	k9 = generarclavek(c, d, 15);
	//cout << "K9:  "<< k9 << endl;

	k10 = generarclavek(c, d, 17);
	//cout << "K10: "<< k10 << endl;

	k11 = generarclavek(c, d, 19);
	//cout << "K11: "<<k11 <<endl;

	k12 = generarclavek(c, d, 21);
	//cout << "K12: "<< k12 <<endl;

	k13 = generarclavek(c, d, 23);
	//cout << "K13: "<<k12 << endl;

	k14 = generarclavek(c, d, 25);
	//cout << "K14: "<<k14 <<endl;

	k15 = generarclavek(c, d, 27);
	//cout << "K15: "<<k15 << endl;

	k16 = generarclavek(c, d, 28);
	//cout << "K16: "<<k16 << endl;


	suma += k1; suma += k2; suma += k3; suma += k4;
	suma += k5; suma += k6; suma += k7; suma += k8;
	suma += k9; suma += k10; suma += k11; suma += k12;
	suma += k13; suma += k14; suma += k15; suma += k16;
	///cout << suma << endl;
	return suma;
}

string clavedef(int num, string base){
    string semilla=sumatodoslosk(base);
    cout<<"El string sumado es de "<<semilla.size()<<" digitos"<<endl;

    int entero=num/semilla.size();
    cout<<"Veces enteras= "<<entero<<endl;

    int resto=moduloint(num, semilla.size() );
    cout<<"Modulo= "<<resto<<endl;
    cout<<"Veces adicionales= "<<resto<<endl;

    string darling;
    for(int i=0; i<entero; i++)
        darling+=semilla;
    for(int i=0; i<resto; i++)
        darling+=semilla[i];

    return darling;
}

int main(){
    string algo;
    ZZ a,b;
    a=250;//numnero i bits
    b=5;
    RC4 p1= RC4(a,b);
    algo=p1.principal();
    ///DES///////////////////////////////////////////////////////////////////////////////////////
    cout<<endl<<endl<<endl<<"string to DES"<<endl<<algo<<endl;
    int clave=1024;
    ZZ temp;
    string def=clavedef(clave, algo);
    cout<<"La clave de "<<clave<<" seria: "<<endl;
    cout<<def<<endl<<endl<<endl;
    cout<<"Y convertido a numero seria: "<<endl;
    cout<<convertirbit(def);
    return 0;
}

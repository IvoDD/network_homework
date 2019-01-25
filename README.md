# network_homework

Файлът server.c е модификация на предоставения от асистента select_chatServer.c.

Модификациите са както следва:
1. Добавени масиви stage и names, които пазят съответно на кой етап от комуникацията е връзката със съответния user и името на потребителя:
```c
char* names[1024]; //name list
int stage[1024]; //stage list 0 - not yet send enter name, 1 - send enter name but not recieved name, 2 - recieved name and processing input
```
2. Изпращане на съобщение "Enter name:" при връзка:
```c
if (send(newfd, "Enter name:\n", 12, 0) == -1){
    perror("send");
}
stage[newfd] = 1; // next stage
```
3. Ако stage[i] е 1, то вместо да препращам съобщението записвам в names[i]:
```c
if (stage[i] <= 1){ //if name not read
    names[i] = malloc(256);
    strcpy(names[i], buf);
    names[i][strlen(names[i])-2] = 0; //remove new line
    ++stage[i]; // got to next stage
}
```
4. Конструиране на ново съобщение преди изпращане и пращане на msg вместо buf:
```c
strcpy(msg, names[i]);
strcat(msg, " says: ");
strcat(msg, buf);
nbytes = strlen(msg);
```
5. При изход на потребител освобождаване на паметта и възстановяване на stage:
```c
if (stage[i] > 1) free(names[i]); // if name written, delete it
stage[i] = 0; // reset stage
```

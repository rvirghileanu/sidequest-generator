**SideQuest Generator**  
*Un tracker de productivitate care tratează viața de zi cu zi ca pe un joc*

Proiect Cloud Computing  \|  Student: Vîrghileanu Maria-Roberta  \|  Platformă: Microsoft Azure

**IDEEA**

Fiecare dintre noi are o listă de lucruri pe care le tot amână: curățenia din weekend, capitolul de citit pentru examen, antrenamentul de joi. Aplicațiile clasice de to-do le notează corect, dar nu prea te fac să vrei să le bifezi. Pe de altă parte, aceiași oameni pot petrece ore întregi într-un joc ca să urce un nivel sau să obțină un item care nu există în realitate. SideQuest Generator pornește de la această contradicție și aplică mecanica jocurilor pe sarcinile reale.

Fiecare task devine un quest, cu dificultate, termen limită și recompensă. Când îl termini, primești XP, bani și, din când în când, un item cadou. Personajul tău crește odată cu progresul real și poți avansa împreună cu prietenii, colegii de cameră sau de grupă.

**CUM ARATĂ PENTRU UTILIZATOR**

* **Quest-uri personale:** utilizatorul își adaugă task-urile și le alege dificultatea (ușor, mediu, greu); recompensa în XP și bani este calculată automat.

* **Progres și inventar:** XP-ul adunat duce la creșterea nivelului, banii se pot cheltui pe itemi cosmetici, iar loot-ul rar apare mai ales la quest-urile grele.

* **Guild-uri:** echipe mici, cu clasament intern și obiective comune (de exemplu „10 antrenamente săptămâna asta”), la care contribuie fiecare membru.

* **Notificări live:** când cineva din guild își încheie un quest, ceilalți primesc imediat un mesaj în aplicație.

**ARHITECTURA PE AZURE**

| Componentă | Serviciu | Rol în aplicație |
| :---- | :---- | :---- |
| **Compute** | Azure App Service (ASP.NET Core, .NET) | Găzduiește interfața web și API-ul REST; conține logica de joc (calculul recompenselor, level-up, validarea acțiunilor). |
| **Storage** | Azure SQL Database | Păstrează utilizatorii, quest-urile, itemii, inventarele și guild-urile; accesul se face prin Entity Framework Core. |
| **Identitate** | ASP.NET Core Identity \+ login Google / Microsoft (OAuth2, OpenID Connect) | Conturi locale cu parole stocate ca hash sau autentificare prin furnizori externi. |
| **Timp real (opțional)** | Azure SignalR Service | Trimite notificări către membrii unui guild fără ca pagina să fie reîncărcată. |

**SECURITATE**

**Autentificarea** se face prin ASP.NET Core Identity, cu opțiunea de login prin Google sau Microsoft. **Autorizarea** este gestionată prin politici și roluri: un utilizator poate vedea și modifica doar propriile quest-uri, iar într-un guild există rolurile de Leader și Membru, doar liderul putând invita sau elimina membri și crea obiective comune. **Baza de date** nu este expusă public: firewall-ul Azure SQL acceptă conexiuni doar dinspre App Service, deci orice citire sau scriere trece obligatoriu prin API. Comunicarea se face exclusiv prin HTTPS, iar connection string-ul este păstrat în setările App Service, nu în cod.

**CUM ACOPĂR CERINȚELE MINIME**

| Cerință | Soluția din proiect |
| :---- | :---- |
| **Cel puțin o resursă de compute** | Azure App Service pentru backend (plus Azure SignalR Service, opțional). |
| **Cel puțin o resursă de storage** | Azure SQL Database pentru toate datele aplicației. |
| **Aplicație securizată** | Baza de date este accesibilă doar din App Service, prin regulile de firewall; HTTPS obligatoriu. |
| **Autentificare \+ autorizare** | Login local sau Google / Microsoft; roluri și verificări de proprietate pe quest-uri și guild-uri. |

**Plan de livrare.** Versiunea de bază include conturile, quest-urile, sistemul de recompense și guild-urile. Notificările prin SignalR, magazinul de itemi și achievement-urile sunt extinderi pe care le adăugăm după ce nucleul aplicației funcționează stabil.

# Teoria LDAP
Aquí trobareu tant la teoria com les estructures dels arxius per .ldif, així com les diferents opcions més importants de les comandes principals.
## Index
1. [Què és LDAP?](#Què-és-ldap?)
2. [Elements de l'LDAP](#elements-de-lldap)
3. [ldapsearch - comanda i opcions](#ldapsearch-comanda-i-opcions)
3. [ldapadd - estructura bàsica del fitxer](#ldapadd-estructura-bàsica-del-fitxer)
4. [ldapadd - comanda i opcions](#ldapadd-comanda-i-opcions)
5. [ldapmodify - estructura bàsica del fitxer](#ldapmodify-estructura-bàsica-del-fitxer)
6. [ldapmodify - comanda i opcions](#ldapmodify-comanda-i-opcions)

## Què és LDAP?
LDAP correspon a les sigles de Lightweight Directory Access Protocol o Protocol d’Accés a Directoris Lleugers.
Podem entendre el sistema LDAP com una base de dades, tot i que la seva estructura i funcionament és molt distinta d’una convencional. L’estructura de LDAP està **optimitzada per a realitzar lectura**, però les funcions d’escriptura son lentes.

A l’LDAP la informació està **estructurada en forma d’arbre**. Aquest arbre s’anomena arbre d’informació del directori o DIT (Directory Information Tree).

![Esquema d'arbre LDAP](Imatges/EsquemaArbreLDAP.png)

| | |
| :-- | :--: |
| Tradicionalment, aquesta estructura ha reflectit límits geogràfics i organitzatius. A la part superior de l’arbre s’han representat els països, i a sota, els estats, les organitzacions , les unitats organitzatives, les persones, els ordinadors, les impressores, els documents o altres conceptes. | ![Esquema d'arbre LDAP](Imatges/EsquemaTradicionalLDAP.png) |
| L’arbre del directori també es pot estructurar en funció dels noms de domini d’Internet (DNS). Aquesta pràctica està cada vegada més estesa, ja que permet trobar un servidor LDAP realitzant una consulta DNS. Aquest tipus de representació és el més habitual a dia d’avui a l’hora d’implementar serveis de directori per administrar dominis informàtics. | ![Esquema d'arbre LDAP](Imatges/EsquemaActualLDAP.png) |

## Elements de l'LDAP
- **Arrel**: és l’equivalent al que coneixiem com a **domini**. El seu nomenament serà del tipus example.com. No obstant, estarà separat de la següent manera als formats de LDAP: dc=example,dc=com
- **Unitats Organitzatives**: Al igual que a windows server, utilitzem les UO per organitzar la informació. La diferència, aquesta vegada el sistema si utilitza l’estructura de UO per l’**organització de l'arbre** (BDD).
- **Objectes**: Són les **puntes *finals* o *fulles* de l'arbre**, tot i que poden tenir subtipus. Contenen la informació de cadascun dels elements. Són semblants als objectes que s’utilitzen a programació. Contenen els atributs característics de l'objecte.
- **Entrada**: Cada node (*fulla*) de l’arbre s’anomena entrada. Una entrada és una **col·lecció d’atributs identificada per un nom distintiu** (distinguished name o DN). Aquest DN és únic al directori i fa referència a l’entrada de manera unívoca (de la mateixa manera que un identificador caracteritza de manera unívoca un registre en una base de dades relacional).
Aquest nom representa la **ruta des de la posició de l’entrada fins a l’arrel** de l’arbre (base, segons la nomenclatura LDAP). Com que se suposa que un directori es fa servir per emmagatzemar els objectes d’una organització determinada, la base serà la ubicació d’aquesta organització. Així, la base es converteix en el sufix de totes les entrades del directori.
- **Atributs**: **Les entrades estan formades per un conjunt d’atributs**. Cada atribut d’una entrada té un tipus i un valor (SINGLE-VALUE) o més (MULTI-VALUE, que és el comportament predeterminat). El tipus d’atribut té associat els valors permesos i el seu comportament a l’hora de fer comparacions, ordenacions o treballar amb subcadenes.
Els tipus són habitualment cadenes de text mnemòniques, com poden ser cn per fer referència a common name (nom comú), sn a surname (cognom) o mail a email address (adreça de correu electrònic).
La sintaxis dels valors dependrà del tipus de l’atribut, per exemple, l’atribut cn podria contenir el valor “Marc Freixas”, l’atribut mail podria contenir un valor com “mfreixas@example.com” i un atribut jpegPhoto contindria una imatge en format binari.

    - [ ] *Podeu trobar exemples i referències a l'apartat [ldapadd - estructura bàsica del fitxer](#ldapadd-estructura-bàsica-del-fitxer).*

## ldapsearch - comanda i opcions
 ```
 ldapsearch -x -LLL -b "dc=example,dc=com" -H "ldap://IP" '(atribut=filtre)'
 ```
- 1. ldapsearch és l'ordre per realitzar cerques a l'LDAP server.
- 2. El retorn serà per STANDARD_OUTPUT en text pla formatat en ldif.
- 3. Les opcions:
	```
	-x Indica que utilitzarà l'identificació simple
	-LLL Formata el contingut eliminant informació comentada.
	     La 1ª L força el format a ldifV1.
	     La 2ª L elimina comentaris.
	     La 3ª desabilita la impressió de la versió.
	-b Fa referència a la base de dades, arrel o punt en el que es realitzarà la cerca.
	-H S'utilitza per tal de realitzar la petició a una màquina remota. Cal indicar el protocol ldap.
	'(atribut=filtre)' Es poden realitzar filtratges per tipus d'objectes o atributs concrets. Per exemple '(objectClass=posixGroup)'.
	```
*Per més referències podeu consultar la [web](https://linux.die.net/man/1/ldapsearch) o el man al terminal.*

## ldapadd - estructura bàsica del fitxer
### Unitat Organitzativa
- dn: ou=**NOM**,*dn superior*
- objectClass: organizationalUnit
- objectClass: top
- ou: **NOM**

-Exemple
 ```
 dn: ou=patata,ou=hort,dc=exemple,dc=com
 objectClass: organizationalUnit
 objectClass: top
 ou: patata
 ```

### Grup
- dn: cn=*NOM**,*dn superior*
- gidNumber: 500
- objectClass: posixGroup
- objectClass: top
- cn: **NOM**
- memberUid: usuari

    - [ ] gidNumber és un identificador únic al sistema. Comencen en 500.

    - [ ] memberUid pot haver més d'un. Indica que l'usuari és part del grup.

-Exemple
 ```
 dn: cn=verdures,ou=hort,dc=exemple,dc=com
 gidNumber: 500
 objectClass: posixGroup
 objectClass: top
 cn: verdures
 memberUid: usuari1
 memberUid: usuari2
 ```

### Usuari
- dn: cn=**NOM I COGNOM**,*dn superior*
- givenName: **NOM**
- gidNumber: 500
- homeDirectory: /home/users/**uid**
- sn: **COGNOM**
- loginShell: /bin/sh
- objectClass: inetOrgPerson
- objectClass: posixAccount
- objectClass: top
- uidNumber: 1000
- uid: **NOMCOGNOM**

    - [ ] gidNumber és el grup principal de l'usuari. Ha de ser un grup existent previament.

    - [ ] homeDirectory és el directory home al sistema de l'usuari.

    - [ ] loginShell és el terminal predeterminat per l'usuari. Pot modificar-se.

    - [ ] uidNumber és un identificador únic al sistema. Comencen en 1000.

    - [ ] uid correspón a l'identificador de l'usuari al sistema de login. Sol crear-se amb la inicial del nom i el cognom. També ha de ser únic.

 ```
 dn: cn=tomaca cherry,ou=hort,dc=exemple,dc=com
 givenName: tomaca
 gidNumber: 500
 homeDirectory: /home/users/tcherry
 sn: cherry
 loginShell: /bin/sh
 objectClass: inetOrgPerson
 objectClass: posixAccount
 objectClass: top
 uidNumber: 1000
 uid: tcherry
 ```

## ldapadd - comanda i opcions
 ```
 ldapadd -c -x -D "cn=admin,dc=example,dc=com" -w "password" -f fitxer.ldif
 ```
- 1. ldapadd és l'ordre per realitzar importacions a l'LDAP server, tant per a creació com modificació.
- 2. Els fitxers a importar han d'estar en format .ldif.
     - 2.1 evidentment han de mantenir internament el format de ldif (LDAP).
- 3. Les opcions:
	    ```
	    -x Indica que utilitzarà l'identificació simple
	    -D Indica que es va a realitzar un bind (login) amb un usuari. Com farem un import cal que sigui un usuari en permisos d'administrador.
	    -w És el password.
	    -f És el fitxer a importar.
	    -c Activa el mode d'operació continua. Notifica els errors però continua executant.
	    ```

## ldapmodify - estructura bàsica del fitxer
- 1. Target
- 2. Changetype
- 3. Cosa a fer i atribut a modificar
- 4. Nou valor de l'atribut

- ### Modificar un atribut
	```
	dn: cn=platan canari,ou=Users,ou=Francia,dc=edt,dc=org
	changetype: modify
	replace: gidNumber
	gidNumber: 500
	```

- ### Afegir un atribut
	```
	dn: Desenvolupadors de software,ou=Group,ou=Barcelona,dc=edt,dc=org
	changetype: modify
	add: memberUid
	memberUid: ruser
	```

- ### Esborrar un atribut
	```
	dn: Desenvolupadors de software,ou=Group,ou=Barcelona,dc=edt,dc=org
	changetype: modify
	delete: memberUid
	memberUid: ruser
	```

- ### Esborrar completament un objecte
	```
	dn: cn=grup2,ou=Group,ou=Barcelona,dc=edt,dc=org
	changetype: delete
	```

## ldapmodify - comanda i opcions
 ```
 ldapmodify -c -x -D "cn=admin,dc=example,dc=com" -w "password" -f fitxer.ldif
 ```
- 1. ldapmodify és l'ordre per realitzar modificacions a l'LDAP server, tant per a creació com modificació.
- 2. Els fitxers a importar han d'estar en format .ldif.
     - 2.1 evidentment han de mantenir internament el format de ldif (LDAP).
- 3. Les opcions:
	    ```
            -x Indica que utilitzarà l'identificació simple.
            -D Indica que es va a realitzar un bind (login) amb un usuari. Com farem un import cal que sigui un usuari en permisos d'administrador.
            -w És el password.
            -f És el fitxer a importar.
            -c Activa el mode d'operació contina. Notifica els errors però continua executant.
	    ```

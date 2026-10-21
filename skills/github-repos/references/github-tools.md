# GitHub įrankiai ir pilnumo patikra

Įrankių sutartys patikrintos 2026-10-21. Prieš vykdydamas remkis dabartine
aplinkos schema: čia pateikti pavadinimai nėra garantija, kad kitoje aplinkoje
jie sutaps. Skill reikia autentifikuotos GitHub integracijos, bet ne naršyklės,
terminalo, Python ar rašymo teisių. Vien katalogo įkėlimas į GitHub jo
neįdiegia į agento skill biblioteką.

## Pagrindiniai veiksmai

| Tikslas | Įrankis šioje aplinkoje | Pastaba |
| --- | --- | --- |
| Dabartinė paskyra | `GitHub.get_user_login()` | Naudok grąžintą `login`, ne atmintį. |
| Visos pasiekiamos repozitorijos | `GitHub.list_repositories` | `page_size`, nulinis `page_offset`; `owner` pagal nutylėjimą praleisk. |
| Vienos instaliacijos sąrašas | `GitHub.list_repositories_by_installation` | Tik jei reikia tokios apimties; būtinas tikras `installation_id`. |
| Pasiekiamos instaliacijos | `GitHub.list_installations` | Reikalingas tik instaliacijomis apriboto sąrašo keliui; patikrink visas pagal dabartinę sutartį. |

Kai schemos neįkeltos, naudok aplinkos įrankių atradimą. Šioje aplinkoje tai
`api_tool.list_resources(paths=["GitHub"], query="list_repositories")`.
Po atradimo iškviesk patį skaitymo įrankį: vien schemų gavimas nėra patikra.

Pradinė užklausa:

```json
{"page_size": 100, "page_offset": 0, "include_search_index_status": false}
```

`include_search_index_status` nereikalingas inventoriui: kodo indeksavimo būsena
neturi nulemti, ar repozitorija patenka į sąrašą.

## Atsakymo forma ir laukai

Pavyzdys sintetinis, ne išsaugotas konkrečios paskyros inventorius:

```json
{
  "result": {
    "repositories": [
      {
        "id": "101",
        "repository_full_name": "demo/example",
        "owner": {"login": "demo"},
        "display_url": "https://github.com/demo/example",
        "visibility": "private",
        "default_branch": "main",
        "archived": false
      }
    ]
  },
  "error": null
}
```

Aplinkos atsakymas gali būti jau išpakuotas iki `repositories`. Tikrink grąžintą
struktūrą; neinterpretuok trūkstamo `result` ar klaidos kaip tuščio masyvo.
Vardui naudok `repository_full_name`, kitose sąsajose gali būti `full_name`.
Nuorodai naudok `display_url` arba `html_url`. Tik jei nė vieno nėra, ją sudaryk
iš patvirtinto GitHub pagrindinio adreso ir pilno vardo; išgalvotų URL nenaudok.
Jei trūksta privalomo repozitorijos vardo, duomenis pažymėk kaip nepilnus,
neprarask įrašo tyliai.

## Poslinkio algoritmas

Laikyk `offset`, visus gautus įrašus ir ankstesnių puslapių identifikatorius.
Po sėkmingo netuščio puslapio atlik `offset += len(raw_repositories)`.
Tai skaičius prieš filtravimą ir dublikatų šalinimą. Jei paprašius 100
įrašų grįžta tik 20, kitas poslinkis yra 20, ne 100.

Pilno 205 įrašų sąrašo pavyzdys: poslinkiai 0, 100, 200, 205; puslapių
dydžiai 100, 100, 5, 0. Baik po patvirtinto tuščio puslapio. Dublikatai
neturi mažinti poslinkio prieaugio. Tiksliai pasikartojantis visas netuščias
puslapis ties nauju poslinkiu reiškia nepatikimą puslapiavimą: sustok su
`nepilnas`, o ne sukis cikle. Atskirų repozitorijų pasikartojimas tarp skirtingų
puslapių yra dublikatų šalinimo, ne ankstyvo stabdymo priežastis.

Jei sąsaja grąžina aiškų tęsinio žymeklį arba galutinį puslapio požymį,
naudok jį pagal sutartį. Įprastos GitHub REST `page` reikšmės yra puslapių
numeriai; jų nekeisk šios integracijos nuliniu `page_offset`.

## Prieigos ir klaidų ribos

GitHub App gali būti suteikta prieiga tik prie pasirinktų repozitorijų.
Sėkmingai užbaigtas integracijos sąrašas nėra įrodymas, kad integracija mato
visas vartotojui kitais būdais prieinamas repozitorijas. Tuščio rezultato,
atšaukto prisijungimo, neleistino veiksmo ir dalinio rezultato nesuplak.

Šios aplinkos bendras `GitHub.fetch` palaiko tik patvirtintus repozitorijų
adresus ir nepalaiko vartotojų ar organizacijų API šeimų. Nesiųsk jam
`https://api.github.com/user/repos` ir nebandyk apeiti apribojimo. Rinkis
specializuotą sąrašo įrankį. Jei palaikomo kelio nėra, aiškiai pranešk
apie trūkstamą galimybę ir ryšio poreikį, o ne išgalvok rezultatą.

## Dokumentacijos šaltiniai

- [Projekte esantis skill-creator: kūrimo eiga, glaustos instrukcijos ir testai](https://github.com/Gytisj/testing-skills/blob/c32b258e5e01e56820797a021bb91751e98c1b5c/skill-creator/SKILL.md).
- [Projekte esanti evals.json schema](https://github.com/Gytisj/testing-skills/blob/c32b258e5e01e56820797a021bb91751e98c1b5c/skill-creator/references/schemas.md).
- [GitHub REST puslapiavimo dokumentacija](https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api).
- [GitHub App prieigos pasirinkimas](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party).

GitHub dokumentacija paaiškina bendrus principus; konkrečių integracijos parametrų ir atsakymų autoritetas yra dabartinė įrankio schema.

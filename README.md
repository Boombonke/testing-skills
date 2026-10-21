# testing-skills

Šioje repozitorijoje laikomi pakartotinai naudojami agentų įgūdžiai (skills). **Visi nauji įgūdžiai privalo būti kuriami tik `skills/<skill-name>/` kataloge**, o ne repozitorijos šaknyje.

## Privalomos taisyklės įgūdžius kuriantiems agentams

1. **Vienas įgūdis = vienas atskiras aplankas:** `skills/<skill-name>/`. Aplanko pavadinimas turi būti trumpas, aiškus, mažosiomis lotyniškomis raidėmis, žodžius atskiriant brūkšneliais (`kebab-case`), pvz., `github-repos` ar `meeting-notes`. Nenaudok tarpų, `skills/skills/` ar įgūdžio aplanko repozitorijos šaknyje.
2. **Pavadinimai turi sutapti:** `skills/<skill-name>/SKILL.md` YAML antraštės `name:` reikšmė privalo tiksliai sutapti su aplanko `<skill-name>` pavadinimu. Nekurk dviejų to paties įgūdžio versijų skirtinguose aplankuose.
3. **Privalomi failai:** kiekvienas įgūdis turi turėti `SKILL.md` (su YAML laukais `name` ir `description`, po jų – aiškios vykdymo instrukcijos) ir `agents/openai.yaml` (agento sąsajos metaduomenys). Aprašyme nurodyk, ką įgūdis daro ir kada jį naudoti.
4. **Pagalbiniai failai – tik to paties įgūdžio viduje:** vykdomą kodą laikyk `scripts/`, dokumentaciją `references/`, šablonus ir kitus išvesties resursus `assets/`, testinius scenarijus `evals/`. Kurk tik reikalingus aplankus, o nuorodas iš `SKILL.md` rašyk santykiniais keliais.
5. **Prieš kurdamas patikrink esamą struktūrą:** peržiūrėk `skills/` ir nustatyk, ar toks įgūdis jau egzistuoja. Jeigu egzistuoja, atnaujink jį tame pačiame aplanke; nekurk alternatyvaus kelio ar dubliavimo.
6. **Prieš įkeldamas pakeitimus patikrink:** visi failai yra `skills/<skill-name>/`, `SKILL.md` egzistuoja, YAML teisingas, `name` sutampa su aplanku, visos nuorodos į `scripts/`, `references/` ir `assets/` veikia, nereikalingi pavyzdiniai failai pašalinti, o pridėti skriptai išbandyti. Jei yra validatorius, paleisk jį prieš commit.
7. **Nekeisk kitų įgūdžių ar bendrų failų be reikalo.** Repozitorijos šaknyje esantis `skill-creator/` yra ankstesnė pagalbinė medžiaga, **ne** šablonas naujo įgūdžio vietai: naujus įgūdžius visada dėk į `skills/`.

## Kanoninė struktūra

```text
testing-skills/
├── README.md
├── skills/
│   ├── .gitkeep
│   └── example-skill/
│       ├── SKILL.md                 # privalomas: name, description, instrukcijos
│       ├── agents/
│       │   └── openai.yaml         # privalomas: sąsajos metaduomenys
│       ├── scripts/                # neprivaloma: vykdomi skriptai
│       ├── references/             # neprivaloma: papildomos instrukcijos
│       ├── assets/                 # neprivaloma: šablonai ir resursai
│       └── evals/                  # neprivaloma: testavimo scenarijai
└── skill-creator/                  # esama pagalbinė medžiaga (ne naujų skills vieta)
```

`example-skill/` šiame medyje yra tik **pavyzdys**, jo kurti nereikia. `skills/.gitkeep` išlaiko `skills/` katalogą „Git“ net tuomet, kai jame nėra įgūdžių.

## Minimalus `SKILL.md` pavyzdys

```markdown
---
name: example-skill
description: Atlieka konkretų veiksmą. Naudok, kai vartotojas prašo šios užduoties.
---

# Example skill

1. Patikrink įvestį ir reikalingus šaltinius.
2. Atlik užduotį.
3. Patikrink ir grąžink rezultatą.
```

Pridėk `agents/openai.yaml` su šį įgūdį atitinkančiais `interface.display_name` ir `interface.short_description` (bei kita reikalinga sąsajos informacija).

## Įgūdžio radimas ir naudojimas

Jei prašoma „panaudok `<skill-name>` skillsą / įgūdį“, pirmiausia ieškok `skills/<skill-name>/SKILL.md`, perskaityk jį ir tik tada, pagal jo nurodymus, atverk reikalingus pagalbinius failus. Nespręsk, kad įgūdžio nėra, vien todėl, kad jis neįdiegtas agento vidinėje įgūdžių bibliotekoje.

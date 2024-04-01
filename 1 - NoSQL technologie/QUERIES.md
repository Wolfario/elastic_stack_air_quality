# 15 Otázek:
>Před zadáním otázek musíte spustit všechny příkazy ze souboru `HOWTO.md`

1. Najděte mezi našimi údaji **3 nejčastějších druhy zvířat**. Zapište druh zvířete a množství.

```js
db.animals.aggregate([
  // Skupina dokumentů podle druhu zvířat a spočítání počtu pro každý druh
  { $group: { _id: "$species", count: { $sum: 1 } } },
  
  // Seřazení výsledků sestupně podle počtu zvířat
  { $sort: { count: -1 } },
  
  // Omezení výsledků na prvních 3 záznamy
  { $limit: 3 }
])
```

2. **Zjistěte počet** všech `Cat, ringtail`, které jsou **středně nebo těžce nemocné** (`minor` nebo `sick`) a také je vypište.

```js
// Vypis takových koček
db.animals.find( { "species" : "Cat, ringtail", $or: [{ "health_status" : "minor" }, { "health_status" : "sick" }] } ).pretty()

// Jejích počet
db.animals.countDocuments( { "species" : "Cat, ringtail", $or: [{ "health_status" : "minor" }, { "health_status" : "sick" }] } )
```

3. **Přidejte nový atribut** `age` pro všechna zvířata v naší databázi, což bude znamenat, **jak staré je zvíře** k *01.01.2023* a **vypište nejstarší zvíře**.

```js
// Aktualizace mnoha dokumentů v kolekci "animals" pomoci updateMany
db.animals.updateMany(
   {},
   [
      {
         // Přidání nového pole "age" do každého dokumentu
         $addFields: {
            "age": {
               // Výpočet věku na základě odhadovaného roku narození
               $subtract: [
                  {$year: new Date("2023-01-01")},
                  {$year: "$approx_birth"}
               ]
            }
         }
      }
   ]
)

// Vypis nejstaršího zvířeti
db.animals.find({}).sort({ "age" : -1 }).limit(1)
```

4. **Změňte typ atributu ceny** z `string` na `double`. Také typ atributu **oblíbeného jídla** ze `string` na `array`.

```js
db.animals.updateMany(
   {},
   [
      {
         // Nastavení nového pole "price" s převedením řetězce na číslo
         $set: {
            "price": {
               $toDouble: {
                  $substr: [ "$price", 1, -1 ]
               }
            }
         }
      }
   ]
)

db.animals.updateMany(
   {},
   [
      {
         // Nastavení pole "favorite_dish" s hodnotou z původního pole
         $set: {
            "favorite_dish": ["$favorite_dish"]
         }
      }
   ]
)
```

5. **Uzdravte všechny** `Cat, ringtail` (`health_status`: `healthy`), kteří jsou **středně nebo těžce nemocní** (`health_status`: `minor` nebo `sick`). Poté **přidejte maso všem zdravým kočkám do jejich oblíbených pokrmů**, protože kočky by přece měly maso milovat (push `meat` do `favorite_dish`).

```js
db.animals.updateMany(
   { 
      // Pokud je druh zvířete "Cat, ringtail" a zdravotní stav je buď "minor" nebo "sick"
      "species": "Cat, ringtail", 
      "health_status": { $in: ["minor", "sick"] } 
   },
   {
      // Nastavení nového zdravotního stavu na "healthy"
      $set: {
         "health_status": "healthy"
      }
   }
)

db.animals.updateMany(
   // Pokud je druh zvířete "Cat, ringtail", zdravotní stav je "healthy" a "favorite_dish" již neobsahuje "meat".
   { 
      "species": "Cat, ringtail", 
      "health_status": "healthy",
      "favorite_dish": { $nin: ["meat"] }
   },
   {
      // Přidání oblíbeného jídla "meat" do pole "favorite_dish" :D
      $push: {
         "favorite_dish": "meat"
      }
   }
)
```
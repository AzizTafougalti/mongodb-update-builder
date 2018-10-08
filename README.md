# mongodb-update-builder
mongodb-query-builder is a base function that allows you to create an update query based on the differences between the old version of a document and the new one. these changes are provided by deep-object-diff's diff function.
# Use
```
let oldGame = {
    name: "DarkSouls",
    rating: "9/10",
    awards: [
        { Nominee: "3A Games Award", award: "Best Strategy Game" },
        { Nominee: "Satellite Award", award: "Outstanding Role Playing Game" },
        { Nominee: "IGN", award: "Best Role Playing Game" }
    ]
}

let newGame = {
    name: "Demon's Souls",
    rating: "10/10",
    awards: [
        { Nominee: "3A Games Award", award: "Best Role Playing Game" },
        { Nominee: "IGN", award: "Best Role Playing Game" }
    ]
}
```
### The deep-object-diff's diff function outputs this:
```
{ name: 'Demon\'s Souls',
  rating: '10/10',
  awards:
   { '0': { award: 'Best Role Playing Game' },
     '1': { Nominee: 'IGN', award: 'Best Role Playing Game' },
     '2': undefined } }
```
Ok now that we have the differences how do we tell mongodb how it should update the oldGame document based on these changes well this is when the mongodb-query-builder's GetQueryPayload function comes in.
### Code:
```
let differences = diff(oldGame, newGame)
let payload = GetQueryPayload(differences, oldGame)
```
As you can see the GetQueryPayload takes two arguments the differences and the original document.
### If we inspect payload we get:
```
[{
    '$set':
    {
        name: 'Demon\'s Souls',
        rating: '10/10',
        'awards.0.award': 'Best Role Playing Game',
        'awards.1.Nominee': 'IGN',
        'awards.1.award': 'Best Role Playing Game'
    }
},
{
    '$push': { awards: { '$each': [], '$slice': 2 } }
}
]
```
As you can see its a list of querys. It's your call what you whant to do with this list(put into one object, separate...).

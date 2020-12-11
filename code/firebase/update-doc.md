# Updating a single doc in Firestore (WIP)

Firestore seems to separate data from document references. Usually, you'll use `data()` when reading and `ref` when writing. Here are a few examples of updating a single doc.

## Using .doc() async

```
async () => {
  const snackRef = await firebase.firestore().collection('snacks').doc(docId).get();
  if(snackRef.exists) {
    await snackRef.update({
      field: newValue
    });
  }
}
```

## Using .where() async

```
async () => {
  const snackQuery = await firebase.firestore().collection('snacks').where('field', '==', 'value').limit(1).get();
  if (!snackQuery.empty) {
    await snackQuery.docs[0].ref.update({
      field: newValue
    });
  }
}
```

## Considerations

If you are working with a single document, it's usually better to use `.doc()` instead of a more complex `.where()` query. I've added checks to see if the document exists before updating but no error handling.

## Use Cases

[Hackernoon](https://hackernoon.com) - We use Firestore at Hackernoon for most of our data needs at Hackernoon. Updating a database doc is the bread and butter if most apps. 

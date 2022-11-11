# Felte setData error repo

This project shows a problem in felte validation when using `setData` on array fields where the errors are not properly updated.

## Reproduction

1. `yarn install`
2. `yarn dev`
3. Click "add Interest"
4. type in `a` into the new input field

## Expected

`errors` should show an error in `items[0].title`

## Actual

`errors` is still `items: [{title: null}]

## More context

The problem is that we dont use a native `input` field to set the form data but instead use `setData` to update the form.

The validator is called and does indeed set 
```json
{
    "email": [
        "Must not be empty",
        "Must be a valid email"
    ],
    "items": [
        {
            "title": [
                "title 0 must have length >= 2"
            ]
        }
    ]
}
```

but the new validation error object never shows up in the `$errors` store from felte.
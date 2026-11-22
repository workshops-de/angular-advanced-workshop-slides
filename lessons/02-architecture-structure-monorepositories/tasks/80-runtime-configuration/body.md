The API address is a constant in `BooksClient`. Every stage needs its own build. Move it into a JSON file that the pipeline can replace after the build, so one artifact runs everywhere.

- **Create the config file** The file belongs to the `books` slice and must be part of the build output unchanged. Create `config/books.json` in the `public` folder of `bookmonkey-client`. It holds the key `apiUrl{:json}` with the current address.

---

- **Read it in the slice** Create a service `BooksConfig` in `feature-books`. It loads the file with `httpResource(){:ts}`. Describe the content with a Valibot schema that accepts only a valid URL for `apiUrl{:ts}`. A broken file must not reach the rest of the code.

---

- **Use it in the client** Remove the constant from `BooksClient`. Read the address with `value(){:ts}` of the config resource. As long as the file is not loaded, the request of a resource must stay idle: return `undefined{:ts}` from its request function.

---

- **Fix the test** `books-client.spec.ts` needs a configuration now. Provide a stub for `BooksConfig` and run `nx test feature-books{:bash}`.

---

- **Change the address without a build** Start the app with `nx serve bookmonkey-client{:bash}` and open the _Network_ tab. Change `apiUrl{:json}` in the JSON file and reload. The requests go to the new address. Then enter a value that is not a URL and find out what happens.

---

- **Write down the decision** Add `docs/adr/0002-runtime-configuration.md`: build-time `environment` files, one global JSON or one JSON per slice? Then run `nx run-many -t lint build test{:bash}` and **commit** your work.

---

- **Think about registration** Why does the app not provide the configuration in `app.config.ts`? Run `nx lint bookmonkey-client{:bash}` after a try and read the message.

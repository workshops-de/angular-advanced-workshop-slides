- **Wait for the component**

```ts
beforeEach(async () => {
  TestBed.configureTestingModule({ providers: [BookMarkerStore] });

  fixture = TestBed.createComponent(BookCard);
  fixture.componentRef.setInput('book', book);
  await fixture.whenStable();
});
```

---

- **Poll instead of asserting once** `expect.poll{:ts}` takes a function. Pass the value you want to check, then chain the usual matcher.

```ts
it('should display the book title', async () => {
  const title = fixture.nativeElement.querySelector('h3');

  await expect.poll(() => title.textContent).toContain(book.title);
});

it('should display the book author', async () => {
  const author = fixture.nativeElement.querySelector('h4');

  await expect.poll(() => author.textContent).toContain(book.author);
});
```

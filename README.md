# AEA03 - Depuració i Millora d'una API REST

**Grup:** mSara Tamurejo i Irie Yamashita  

## Objectiu principal
Aplicar els coneixements de Node.js, Express i API REST per analitzar, corregir i millorar un codi amb errors en una API real.


## Errors i solucions
A continuació una llista dels errors que hem identificat i les solucions que proposem:

1. `GET` - No comprova si el llibre amb l'**id** proporcionat existeixi  
    **Solució:** 
    Afegir un condicional, si troba el llibre li retornarà les dades del llibre, sinó li retornem la resposta amb un codi `404 NOT FOUND` i un missatge d'error.
    ```js
        if (book) {
            res.json(book);
        } else {
            res.status(404).json({ message: `No existe un libro con el id ${id}!` })
        }
    ```

2. `POST` - No comprova si la petició té un format correcte, és a dir, si conté totes les dades necessàries
    **Solució:**  
    Afegir una comprovació per si falta algun dels camps abans d'afegir el llibre. Retornem una resposta amb codi `400 BAD REQUEST`.
    ```js
        if(!body.name || !body.author || !body.year ) {
            return res.status(400).json({ message: `Petición mal hecha. Te faltan campos {name, author, year}`});
        }
    ```

3. `POST` - No comprova si ja existeix un llibre amb el mateix títol que el nou  
    **Solució:** 
    Afegir una petita comprovació amb el `some` i un condicional. Així sabem si hi ha un llibre amb el mateix títol.
    ```js
        const encontrado = data.books.some((book) => book.name === name);
        if (!encontrado) {
            const newBook = {
                id: data.books.length + 1,
                ...body,
            };
            data.books.push(newBook);
            writeData(data);
            res.json(newBook);
        } else {
            return res.status(400).json({ message: "El libro ya existe!" })
        }
    ```

4. `DELETE` - No comprova si el llibre existeix abans d'esborrar-lo  
    **Solució:** 
    Abans d'esborrar el llibre, fem una comprovació amb el `findIndex`. Si no troba un llibre amb l'id proporcionat, retorna una resposta amb el codi `404 NOT FOUND`.
    
    ```js
        const bookIndex = data.books.findIndex((book) => book.id === id);
        //Si ha encontrado el id del libro, lo borra
        if (bookIndex !== -1) {
            //splice esborra a partir de bookIndex, el número de elements 
            // que li indiqui al segon argument, en aquest cas 1
            data.books.splice(bookIndex, 1);
            writeData(data);
            res.json({ message: "Book deleted successfully" });
        } else {
            //Si no lo ha encontrado, no lo borra y manda un mensaje de error
            return res.status(404).json({ message: "No se ha podido eliminar el libro, no existe!" });
        }
    ```

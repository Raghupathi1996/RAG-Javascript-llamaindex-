# L2: Build a full-stack web app

You are going to create a very simple web server. 

This server will act as a web API, taking responses from the web and replying with data.

## 1. Make a simple web server


```typescript
const handler = (req) => {
  // Create a new response object
  const body = new TextEncoder().encode("Hello World!");
  return new Response(body, { status: 200 });
};
```

### Start the server on port 8001

**Note**: If ports 8000 or 8001 are unavailable, try using port 8002 or 8003.


```typescript
let server = Deno.serve({ port: 8001 }, handler);
```

### Make a request to the server


```typescript
const response = await fetch("http://localhost:8001")

```


```typescript
await response.text()
```

### Shut down the server


```typescript
await server.shutdown()
```

### Set up the documents, index and query engine


```typescript
import * as mod from "https://deno.land/std@0.213.0/dotenv/mod.ts";
import { 
    Document, 
    VectorStoreIndex, 
    SimpleDirectoryReader 
} from "npm:llamaindex@0.1.8"
const keys = await mod.load({export:true}) // read API key from .env

const documents = await new SimpleDirectoryReader().loadData({directoryPath: "./data"})
const index = await VectorStoreIndex.fromDocuments(documents)
const queryEngine = index.asQueryEngine()
```

### Create a new handler

This new handler will accept a query as input and respond from the query engine.


```typescript
const handler2 = async (req) => {
    if(req.method == "POST") {
        // we'll expect the incoming query to be a JSON object of the form {query: ...}
        let data = await req.json()
        let answer = await queryEngine.query({query: data.query})
        // and our response will be a JSON object of the form {response: ...}
        let responseObj = {
            response: answer.toString()
        }
        return new Response(JSON.stringify(responseObj), { 
            status: 200
        })
    } else {
        return new Response("Not found", { status: 404 })
    }
}
let server2 = Deno.serve( { port: 8002 }, handler2 )
```

### Send a request to the server


```typescript
let data = { query: "How does the author feel about college?" }
```


```typescript
let response2 = await fetch("http://localhost:8002", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(data) // Convert the JavaScript object to a JSON string
})
```


```typescript
let responseObj = await response2.json()
console.log(responseObj.response)
```

### Shut down the server


```typescript
await server2.shutdown()
```

## 2. Make a web app!

In this next section, we're going to use a development version of a web app, which is already setup for you.

- Use the utility functions in **utils.ts** file. 

**Note:** Go to `File` and click on `Open` to access the **utils.ts** file and all the code used in this lesson.


```typescript
import { runFrontend, runBackend, addToFrontend } from './utils.ts'
```

**Note**: When you run the below `runFrontend()` code in your notebook, a link will appear as the output. Click on this link to view the web app shown in the video.


```typescript
runFrontend()
```

**Note**: Please note that the link generated above is specific to your notebook and will display all the upcoming frontend updates in this lesson. Please keep the page of this link open on your browser. Each time, you execute `await addToFrontend("")`, you will see the new changes on that page. Note that you might need to refresh the page to see the changes.

### Run the API server


```typescript
runBackend()
```

### Create a React component that increments a counter


```typescript
// import-me: 1
import React, { useState } from 'https://esm.sh/react@17.0.2';

const Counter: React.FC = () => {
  return (
    <div>
      <h1>Counter: 0</h1>
      <button>Increment</button>
    </div>
  );
};

export default Counter;
```

### Bring it to the app

This command will inject our component into a pre-written app. Check the link from the `runFrontend()` code cell and refresh the page to see the results.


```typescript
await addToFrontend("1")
```

### Add a variable


```typescript
// import-me: 2
import React, { useState } from 'https://esm.sh/react@17.0.2';

const Counter: React.FC = () => {
  const count = 10

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button>Increment</button>
    </div>
  );
};

export default Counter;
```

Run the below to inject the new feature.

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to see the results.


```typescript
await addToFrontend("2")
```

### Add interactivity


```typescript
// import-me: 3
import React, { useState } from 'https://esm.sh/react@17.0.2';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  const increment = () => {
    let newCount = count+1
    setCount(newCount);
  };

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={increment}>Increment</button>
    </div>
  );
};

export default Counter;
```

Inject this new version and see what happens!

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to view the results.


```typescript
await addToFrontend("3")
```

### Make an app that asks a question to the server


```typescript
// import-me: 4
import React, { useState } from 'https://esm.sh/react@17.0.2';

const QuerySender: React.FC = () => {
  return (
    <div>
      <h1>Ask a question</h1>
      <form>
        <input id="query" type="text"/>
        <button type="submit">Query</button>
      </form>
      <div id="answer">Answer will go here</div>
    </div>
  );
};

export default QuerySender;
```

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to view the results.


```typescript
await addToFrontend("4")
```

### Add some variables


```typescript
// import-me: 5
import React, { useState } from 'https://esm.sh/react@17.0.2';

const QuerySender: React.FC = () => {
  const [query, setQuery] = useState<string>('');    

  // Function to update the state with the input value
  const handleChange = (e) => {
    setQuery(e.target.value);
  };
    
  return (
    <div>
      <h1>Ask a question</h1>
      <form>
        <input id="query" type="text" value={query} onChange={handleChange}/>
        <button type="submit">Query</button>
      </form>
      <div id="answer">{query}</div>
    </div>
  );
};

export default QuerySender;
```

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to view the results.


```typescript
await addToFrontend("5")
```

### Add a new pair with "answer  and setAnswer"


```typescript
// import-me: 6
import React, { useState } from 'https://esm.sh/react@17.0.2';

const QuerySender: React.FC = () => {
  const [query, setQuery] = useState<string>('');    
  const [answer, setAnswer] = useState<string>('');

  const handleSubmit = async (e) => {
    e.preventDefault()
    setAnswer(query + " is the answer");
  }
    
  // Function to update the state with the input value
  const handleChange = (e) => {
    setQuery(e.target.value);
  };
    
  return (
    <div>
      <h1>Ask a question</h1>
      <form onSubmit={handleSubmit}>
        <input id="query" type="text" value={query} onChange={handleChange}/>
        <button type="submit">Query</button>
      </form>
      <div id="answer">{answer}</div>
    </div>
  );
};

export default QuerySender;
```

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to view the results.


```typescript
await addToFrontend("6")
```

### Call the back-end


```typescript
// import-me: 7
import React, { useState } from 'https://esm.sh/react@17.0.2';

const QuerySender: React.FC = () => {
  const [query, setQuery] = useState<string>('');
  const [answer, setAnswer] = useState<string>('');

  const handleSubmit = async (e) => {
    e.preventDefault()
    setAnswer("Thinking...")
    const response = await fetch('http://localhost:8000', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ query }),
    });
    const data = await response.json();
    console.log('Response from the server:', data);
    setAnswer(data.response);
  }

  // Function to update the state with the input value
  const handleChange = (e) => {
    setQuery(e.target.value);
  };  

  return (
    <div>
      <h1>Ask a question</h1>
      <form onSubmit={handleSubmit}>
        <input id="query" type="text" value={query} onChange={handleChange} />
        <button type="submit">Query</button>
      </form>
      <div id="answer">{answer}</div>
    </div>
  );
};

export default QuerySender;

```

**Note**: Refresh the page using the link provided by `runFrontend()` code cell to view the results.


```typescript
await addToFrontend("7")
```

### Congratulations, you've made a web app!

Hurrah! You have written a React component that calls out to a server, makes a request, and updates the UI with the answer. 

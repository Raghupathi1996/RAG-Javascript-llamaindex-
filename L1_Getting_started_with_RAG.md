# Lesson 1: Getting started with RAG


Welcome to Lesson 1.

To access the `data` file, go to `File` and click on `Open`.

I hope you enjoy this course!

### Import required dependencies from npm and load the API key


```typescript
import * as mod from "https://deno.land/std@0.213.0/dotenv/mod.ts";
const keys = await mod.load({export:true}) // read API key from .env

```


```typescript
import { 
    Document, 
    VectorStoreIndex, 
    SimpleDirectoryReader 
} from "npm:llamaindex@0.1.8"
```

### Load our data from a local directory


```typescript
const documents = await new SimpleDirectoryReader()
    .loadData({directoryPath: "./data"})
```

### Initialize an index


```typescript
const index = await VectorStoreIndex.fromDocuments(documents)
```

### Create a query engine 

This convenience function combines several components:
- Retriever
- Postprocessing
- Synthesizer


```typescript
const queryEngine = index.asQueryEngine()
```

### Let's ask a question!


```typescript
const response = await queryEngine.query({
    query: "What did the author do in college?"
})
```


```typescript
console.log(response.toString())
```

### But what just happened? let's break it down!

You need an:
- LLM to answer questions
- embedding model to encode them


```typescript
import * as llamaIndex from "npm:llamaindex@0.1.8"
```


```typescript
let customLLM = new llamaIndex.OpenAI()
let customEmbedding = new llamaIndex.OpenAIEmbedding()

```

 Let's put the LLM and the embedding model into a `ServiceContext` object:


```typescript
let customServiceContext = new llamaIndex.serviceContextFromDefaults({
    llm: customLLM,
    embedModel: customEmbedding
})
```

### Let's make our own prompt


```typescript
let customQaPrompt = function({context = "", query = ""}) {
    return `Context information is below.
        ---------------------
        ${context}
        ---------------------
        Given the context information, answer the query.
        Include a random fact about whales in your answer.\
        The whale fact can come from your training data.
        Query: ${query}
        Answer:`
}
```

You need a `ResponseBuilder` that uses our prompt and our service context.


```typescript
let customResponseBuilder = new llamaIndex.SimpleResponseBuilder(
    customServiceContext,
    customQaPrompt
)
```

The `responseBuilder` goes to a `synthesizer`, which also needs a service context.


```typescript
let customSynthesizer = new llamaIndex.ResponseSynthesizer({
    responseBuilder: customResponseBuilder,
    serviceContext: customServiceContext
})
```

You also need a `retriever`.


```typescript
let customRetriever = new llamaIndex.VectorIndexRetriever({
    index
})
```

The `synthesizer` and the `retriever` go to our query engine:


```typescript
let customQueryEngine = new llamaIndex.RetrieverQueryEngine(
    customRetriever,
    customSynthesizer
)
```

### Let's check the response!


```typescript
let response2 = await customQueryEngine.query({
    query: "What does the author think of college?"
})
```


```typescript
console.log(response2.toString())
```

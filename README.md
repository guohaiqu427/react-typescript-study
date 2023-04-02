# components
COMMAND + CLICK TO THE FUNCTION 

1.  file extension for react component: 
        js: .jsx
        ts: .tsx

2.  type: prop inline 
    const NameBadge = ({name}: {name:string}) => {}
    <!-- those two are equal -->
    const NameBadge = (props: {name:string}) => {}
    
3.  type : return value: inline
    const NameBadge = ({name}: {name:string}):JSX.Element => {}

4. config 
    - "allowJs": true,
    - "strict": true,
    - "noFallthroughCasesInSwitch": true,
    
5.  TYPE VS INTERFFACE
    type : 
    interface : class |  extend | overwrite property

6. type: prop type 
    type NameBadgeProp = {
        greeting? : string
        name: string
    }  
    const NameBadge = (props:NameBadgeProp) => {}

7. type: component children 

    type BoxTypes = {
        children : React.ReactNode
    }

    or .... built-in ...

    import {PropsWithChildren} from "react"
    type BoxTypes = PropsWithChildren<{}>
    ==> 
    type QuotesProps = {
        count: number,
    }
    const Quotes = ({children, count}: PropsWithChildren<{QuotesProps}>) => {}

8. state: 

    const [count, setCount] = useState(0)

    type is set to number, ==> initial value is passed as a number. ts figured it out. 

    useState is really a wrapper around reducer

# API calls 
1. defining type for API returned values (single / multi)

    in this case, we do not know the quote type before it returns, so we need to tell ts, it can be "Quote" or "undefined" instead of the initalvalue approach.
    ```js
    export type Quote = {
        id: number;
        content: string;
        source?: string;
    };

    const [quote, setQuote] = useState<Quote | undefined>()
    const [quotes, setQuotes] = useState<Quote[]>([])

    useEffect(() => {
        fetchRandomQuote().then(setQuote);
        fetchRandomQuotes().then(setQuotes);
    }, []);
    ```

2. pass state methods to components 
    ```js
    const parentComponent () => {
        const [quotes, setQuotes] = useState<Quote[]>([]) // grab here!
        return (
            <childComponent setQuotes={setQuotes}>
        )
    }
    ```
    ```js
    type childComponentProps = {
        setQuotes: React.Dispatch<React.SetStateAction<Quote[]>> // grab from parent
    }
    const childComponent = ({setQuotes}: childComponentProps) => {
        setQuotes //use it somewhere
    }
    ```

# useReducer 
1. reudcer type
    ```js 
    // reducer 

    type InitalSate = {
        count:number,
        draftCount: number | string
    }

    const initalState : InitalSate = {
        count: 0 
        draftCount: 0
    }

    type Action = {
        type: "increment" | "decrement" | "reset"
    }

    type ActionWithPayload = {
        type: "updateDraftCount"
        payload: number
    }

    const reducer = (state: initalState, action: Action | ActionWithPayload ) => {
        const { count , draftCount } = state
        if(action.type === " increament") {
            const newCount = count + 1
            return { count: newCount, draftCount:newCount}
        }
        return state
    }

    const [state, dispatch] = useReducer(reducer, initalState)
    ```

2. passing dispatch as prop
    goal: dispatch event in child component's event handler. 
  
    1.  a. pass "dispatch" to child from parent , 
        b. parent export action types. 
        ```js
        <ChildComp dispatch={dispatch} />
        export type Action = {
            type: "increment" | "decrement" | "reset"
            payload: number
        }   

        ```
    2. a. child has to receive "the dispatch prop", b. child define dispatch type with the action imported
        ```js
        type ChildCompProps = {
            dispath: Dispatch<Action>
        }
        ```

    3. in child, use dispatch. Dispatch all the actions defiined in step 1
        at this ponit, dispatch has been passed to child and can be used. 
        ```js 
        // A function that wraps a dispatch action
        const adjustRed = (event: ChangeEvent<HTMLInputElement>) => {   
            dispatch({ type: "increment", payload: +event.target.value });
        };
        ```

# template literal types :
    ```js
    type HexColor = `#${string}`;
    type RgbString = `rgb(${number}, ${number}, ${number})`;

    type ColorFormat = 'rgb' | 'hex' | 'hsl' | 'hsv';
    type ActionType = `update-${ColorFormat}-color`;
    ```

4. Context API

# Context 
**not a state management tool**
1. context
    ```js
    import { createContext } from 'react';
    const ColorContext = createContext({value:"#BADA55"})

    export const colorProvider = ({children}:PropsWithChildren) => {
        return (
            <ColorContext.Provider value = { {hexColor: "#000000"} }> 
                {children}
            </ColorContext.Provider>
        )
    }
    ```
2. context and useReducer 
    context happens outside of react component,  useReducer only happens inside react component 
    define useReducer types in context 
    ```js
    import { createContext } from 'react';
    // solution1: make useReducer type optional 
    const ColorContext = createContext<{ hexColor: string; dispatch?: Dispatch<ColorActions>}>({hexColor: "#000000"})

    // solution2: tell ts, what ever passed in to createcontext, treat it as the defined type. 
    type ColorContextState = {
        hexColor: string; 
        dispatch: Dispatch<ColorActions>
    }
    const ColorContext = createContext<{ColorContextState}>({hexColor:"#BADA55"} as ColorContextState); 


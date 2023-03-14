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
        const [quotes, setQuotes] = useQuote<Quote[]>([]) // grab here!
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

2. passing dispatch as prop (nested twice)
    goal: in the end dispatch to event handler. 
    1. once "intitalState types" and  "Action types" are defined in reducer function, 
    2. and imported reducer fn into parent component, then used to defined useReducer
    3. at this point, with ```const [state,dispath] = useReducer(reducer, initalState)```, state and dispatch has "types"
    4. pass "dispatch" to child from parent 
        ```js
        <ColorSliders {...rgb} dispatch={dispatch} />
        ```
    5. child has to receive "the dispatch prop" and define type in its "type of interface"
        ```js
        interface ColorSidersProps extends RGBColorType {
            dispatch: Dispatch<AdjustmentAction>;   //UNION TYPE
        }
        ```
    6. in child, use dispatch. Dispatch all the actions defiined in step 1
        at this ponit, dispatch has been passed to child and can be used. 
        ```js 
        // A function that wraps a dispatch action
        const adjustRed = (event: ChangeEvent<HTMLInputElement>) => {   
            dispatch({ type: "ADJUST_RED", payload: +event.target.value });
        };
        const adjustGreen = (event: ChangeEvent<HTMLInputElement>) => {
            dispatch({ type: "ADJUST_GREEN", payload: +event.target.value });
        };

        const adjustBlue = (event: ChangeEvent<HTMLInputElement>) => {
            dispatch({ type: "ADJUST_BLUE", payload: +event.target.value });
        };
        ```
    7.  action are noormally dispatch during user interactions, 
        if the user interaction happens in a component nest component of child, 
        we can pass the stored wrap function to the nested component, defined the type for the wrap function
        ```js
        export interface ColorInputProps {
            id: string;
            label: string;
            value: number;
            onChange: (event: ChangeEvent<HTMLInputElement>) => void; //here
        }
        const NestComp ({onchange} : ColorInputProps) =()=> {
            <input onChange={onChange}
        }
      />
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

    ```js
    import { createContext } from 'react';

    const ColorContextState = {
        hexColor: "#BADA55"
        dispatch: Dispatch<ColorActions>   // solution 1
    }

    const ColorContext = createContext<ColorContextState>({hexColor:"#BADA55"} as ColorContextState); // solution 2
    
    export const colorProvider = ({children}:PropsWithChildren) => {
        const [state, dispatch] = useReducer(colorReducer, initalState) // get dispatch that context type needed
        const hexColor = state.hexColor
        return(
                                                                        // give dispatch to provider
            <ColorContext.Provider value = { {hexColor, dispatch} }> 
                {children}
            </ColorContext.Provider>
        )
    }
    ```
    ``` js
    <colorProvider>
        <app />
    </colorProvider>
    ```
    ```js 
    // ...some imports
    const {hexColor, dispatch} = useContext(ColorContext) // import 
    onClick = { ()=> dispatch({type: "....", { payload: {hexColor} }}) } // use it 
    ```

issue: 
    context need to be done outside of react component 
    reducer has to be done in react component 
    to define dispatch type in context, ==> issue

1. make the dispatch property optional, 
    ```js
    dispatch?: Dispatch<ColorActions>
    ```
2. tell ts it is this type even if ts does not know it yet: 
    ```js
    const ColorContext = createContext<ColorContextState>({hexColor:"#BADA55"} as ColorContextState);
    ```


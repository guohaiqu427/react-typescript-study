1. setup 
    1. ```npm i -D typescript@4.8.4```
    2.  typescript compiler: ```npx tsc --init```    
        tsconfig.json
        {
            "compilerOptions": {
                "target": "ES2022",
                "jsx": "react-jsx",
                "module": "ES2022",
                "moduleResolution": "node",
                "esModuleInterop": true,
                "forceConsistentCasingInFileNames": true,
                "strict": true,
                "skipLibCheck": true
            }
        }
    3. react type files: ```npm i -D @types/react@18.0.21 @types/react-dom@18.0.6```

    4. setup eslint
        ```npm i -D eslint-import-resolver-typescript@3.5.1 @typescript-eslint/eslint-plugin@5.40.1 @typescript-eslint/parser@5.40.1``` give eslint the ability to resolve ts file

        ```"lint": "eslint \"src/**/*.{js,jsx,ts,tsx}\" --quiet",``` package.json
        
        ```json
        .eslintrc.json
        // inside extends, above prettier rules
        "plugin:@typescript-eslint/recommended",

        // inside rules, generally a good rule but we're going to disable it for now
        "@typescript-eslint/no-empty-function": 0

        // inside plugins
        "@typescript-eslint"

        // add parser
        "parser": "@typescript-eslint/parser",

        // add to parserOptions
        "project": "./tsconfig.json",

        // replace settings object
        "settings": {
            "react": {
                "version": "detect"
            },
            "import/parsers": {
                "@typescript-eslint/parser": [".ts", ".tsx"]
            },
            "import/resolver": {
                "typescript": {
                "alwaysTryTypes": true
                }
            }
        }
        ```

2. define **children** type 
    ``` ts 
    import { ReactElement } from "react";
    const Modal = ({ children }: { children: ReactElement }) => {}
    ```

3. define **ref** obj 
    ```ts 
    import {  useRef, MutableRefObject } from "react";
    const elRef: MutableRefObject<HTMLDivElement | null> = useRef(null);
    ```

4. define **APIResponse** 
     ```ts
    export type Quote = {
        id: number;
        content: string;
        source?: string;
    };

    export type QuoteAPIResponse {
        // ...
        quotes: Quote[]
    }
    ```

5. define **useQuery** 
    ```ts
    const results = useQuery<QuoteAPIResponse>(["details", id], fetchPet);

    ```
    ```ts
    import { QueryFunction } from "@tanstack/react-query";
    import { PetAPIResponse } from "./APIResponsesTypes";

    const fetchSearch: QueryFunction< PetAPIResponse,[ "search",{ breed: string;} ]> = async function ({ queryKey }) {}
                      "functionType"<"returnValueType" [ "Key", "paramType"       ]>
    ```

6. define **createContext**
    ```ts
    const AdoptedPetContext = createContext<[Pet, (adoptedPet: Pet) => void]>({default: "default"});
    /*  1. give it default value, 
    **  2.  tell ts the context has a value of "type" or a function that takes a pet and return void
    */
    ```

7. define **class component props**
    ```ts
    interface Iprops {
        images: string[]
    }
    class Carousel extends Component<Props> { … }
    ```

8. define **events**
    ```ts
    handleClick = ( event: MouseEvent<HTMLElement>) => {...}
    // tells ts, what event && where it points to

    // common pattern: dance with complier, tell it that the code will give ts HTMLElement
    if(!(event.target instanceof HTMLElement)) {
        return
    }
    // e.target & e.currentTarget  
    ```
    

9. define **ErrorBoundary** 
    ```ts
    import { Component, ErrorInfo, ReactElement } from "react";
    class ErrorBoundary extends Component<{children: ReactElement}> { … }   // children 
    componentDidCatch(error: Error, info: ErrorInfo) {} // error & info 
    ```
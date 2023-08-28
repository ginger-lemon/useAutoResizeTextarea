# useAutoResizeTextarea

紀錄專案使用到的自訂 hook 。

## 空行時增加高度

- 使用字串分割的方式 ``String.prototype.split()``  ：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split

```
function App() {
  const [value, setValue] = React.useState("");
  const rows = value.split("\n").length;
  return (
    <div className="App">
      <textarea
        value={value}
        rows={rows}
        onChange={(e) => setValue(e.target.value)}
      />
    </div>
  );
```

## 自動增加高度

- ref: https://codesandbox.io/s/textarea-auto-resize-react-hngvd?file=/src/index.js

基本上都是靠高度去調，rows 可以預設加在元素內
這種方式就算不將 rows 的資訊存在 local storage 中也可以在重新整理頁面時保留相同的高度

```
// 基本，如果多個 textarea 需要共用相同邏輯可能需要用到 useCallBack
  const resizeTextArea = () => {
    textAreaRef.current.style.height = "auto";
    textAreaRef.current.style.height = textAreaRef.current.scrollHeight + "px";
  };

useEffect(resizeTextArea, [valye]);

<TextArea ref={textAreaRef} value={val} onChange={onChange} rows={1} />
```

＊此方式僅限用於一個 textarea 的情況，如果有複數 textarea 會互相影響。

## 使用 Custom Hook

> textarea 自動調整高度針對節點的樣式，可以回傳節點避免各節點間相互干擾。

```
import { useEffect, useRef } from "react";

function useAutoResizeTextatea(value) {
    const ref = useRef();

    useEffect(() => {
        resizeTextarea();
    }, [value]);

    function resizeTextarea() {
        if (ref.current) {
            ref.current.style.height = 'auto';
            ref.current.style.height = ref.current.scrollHeight + 'px';
        }
    }

    return ref;
}

export default useAutoResizeTextatea;

// 元件內使用：
    const titleInputRef= useAutoResizeTextatea(noteTitle);
    const textsInputRef = useAutoResizeTextatea(noteTexts);
// 因為是回傳節點，因此兩節點的樣式高度不會互相影響
```

本次使用 custom hook 心得：**保持 hook 目的的單純**；回傳的東西都會與 React 提供的基本 hook 有關，避免元件相互影響，以及回到根本的目的。自訂 hook 的目的就是將重複的動作簡化成共用方式，可確認提取 hook 時最上面會是基本 hook 、回傳的東西與基本 hook 有關。
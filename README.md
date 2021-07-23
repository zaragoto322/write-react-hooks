# 实现过程
```bash
import React, { useEffect } from 'react'
import ReactDOM from 'react-dom'

function render() {
  cursor = 0
  ReactDOM.render(
    <App />,
    document.getElementById('root')
  )
}

let memorizedState = []        //声明一个数组，可实现多个useState和useEffects。用于存放state和useEffects中是否检测的参数
let cursor = 0                 //用来表示数组下标

function useState(initValue) {
  memorizedState[cursor] = memorizedState[cursor] || initValue
  const currentCursor = cursor
  function setState(newState) {
    memorizedState[currentCursor] = newState     //经典闭包，currentCursor不变（再次调用时，仍为第一次传递进来的那个数）
    render()
  }
  return [memorizedState[cursor++], setState]
}


function useEffects(callback, depArray) {
  const hasNoDeps = !depArray
  const deps = memorizedState[cursor]
  const hasChangedDeps = deps ? !depArray.every((el, i) => el === deps[i]) : true
  if(hasNoDeps || hasChangedDeps) {
    callback()
    memorizedState[cursor] = depArray
  }
  cursor++
}



function App() {                            //测试用例子
  console.log('render app')
  const [count, setCount] = useState(0)
  const [number, setNumber] = useState(1)

  useEffects(() => {
    console.log('update', count)
  }, [count])
  useEffects(() => {
    console.log('update', number)
  }, [number])

  return (
    <div className="App">
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
          Add count
      </button>

      <p>You clicked {number} times</p>
      <button onClick={() => setNumber(number + 1)}>
          Add count
      </button>
    </div>
  )
}

export default App
```

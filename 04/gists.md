App.js

```js
import { createStore } from "redux";
import rolloutOperationReducer, {
  executeOperation,
  EXECUTE_OPERATION,
} from "./redux/modules/rolloutOperation";

const store = createStore(rolloutOperationReducer);
console.log(store.getState());

let unsubscribe = store.subscribe(() => console.log(store.getState()));
store.dispatch(executeOperation("start"));
store.dispatch(executeOperation("pause"));
store.dispatch({
  type: EXECUTE_OPERATION,
  operation: "abort",
});

unsubscribe();
```

index.js

```js
import { Provider } from "react-redux";
import store from "./redux/store";

<Provider store={store}>
  <App />
</Provider>;
```

redux/store.js

```js
const store = createStore(
  rolloutOperationReducer /* preloadedState, */,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

redux/store.js

```js
import { createStore, combineReducers } from "redux";
import rolloutOperationReducer from "./modules/rolloutOperation";
import statusSelectionReducer from "./modules/statusSelection";

const rootReducer = combineReducers({
  rolloutOperation: rolloutOperationReducer,
  statusSelection: statusSelectionReducer,
});
```

redux/modules/rings

```js
// action type
export const SET_RINGS = "SET_RINGS";

const initialState = {
  rings: [],
};

export default function ringsReducer(state = initialState, action = {}) {
  switch (action.type) {
    case SET_RINGS:
      return {
        ...state,
        rings: action.rings,
      };

    default:
      return state;
  }
}

// action creator
export const setRings = (data) => ({
  type: SET_RINGS,
  rings: data,
});
```

containers/RingTable/RingTable.js

```js
class RingTable extends Component {
  componentDidMount() {
    fetch("https://run.mocky.io/v3/adc0e655-b26f-4738-a0d8-9cc976a8fa36")
      .then((response) => response.json())
      .then((data) => this.props.setRings(data));
  }

  render() {
    return (
      <Table bordered>
        <thead>
          <tr>
            <th></th>
            <th>Rollout Status</th>
            <th>Windows</th>
            <th>Linux</th>
            <th>Unix</th>
          </tr>
        </thead>
        <tbody>
          {this.props.rings.map((row, index) => (
            <tr key={`ring-row-${index}`}>
              <td>{row.target}</td>
              <td>
                <Badge variant={getBadgeVariant(row.status)}>
                  {row.status}
                </Badge>
              </td>
              <td>{row.windows}</td>
              <td>{row.linux}</td>
              <td>{row.unix}</td>
            </tr>
          ))}
        </tbody>
      </Table>
    );
  }
}

const mapDispatchToProps = (dispatch) => ({
  setRings: (data) => dispatch(setRings(data)),
});

export default connect(null, mapDispatchToProps)(RingTable);
```

containers/RingTable/RingTable.js

```js
state = { allData: [], displayedData: [], };

componentDidUpdate(prevProps, prevState) {
  if (prevProps.rings !== this.props.rings) {
    this.setState({
      allData: this.props.rings,
      displayedData: this.props.rings,
    });
  }

  if (
    prevProps.statusToShow !== this.props.statusToShow ||
    prevState.allData !== this.state.allData
  ) {
    const filteredData =
      this.props.statusToShow === "All"
        ? this.state.allData
        : this.state.allData.filter(
            (data) => data.status === this.props.statusToShow
          );
    this.setState({ displayedData: filteredData });
  }

  if (prevProps.rolloutOperation !== this.props.rolloutOperation) {
    const changedData = this.state.allData.map((data) => ({
      ...data,
      status:
        data.status === "Paused" || data.status === "Ongoing"
          ? operationTransition[this.props.rolloutOperation]
          : data.status,
    }));

    this.setState({ allData: changedData });
  }
}
```

```js
const operationTransition = {
  start: "Ongoing",
  pause: "Paused",
  abort: "Aborted",
};
```

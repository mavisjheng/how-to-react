App.js

```js
import { createStore } from "redux";
import { selectAction, SELECT_ACTION } from "./redux/actions/rolloutOperation";
import rolloutOperationReducer from "./redux/reducers/rolloutOperation";

const store = createStore(rolloutOperationReducer);
console.log(store.getState());

let unsubscribe = store.subscribe(() => console.log(store.getState()));
store.dispatch(selectAction("start"));
store.dispatch(selectAction("pause"));
store.dispatch({
  type: SELECT_ACTION,
  action: "abort",
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

store.js

```js
const store = createStore(
  rolloutOperationReducer /* preloadedState, */,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

store.js

```js
import { createStore, combineReducers } from "redux";
import rolloutOperationReducer from "./reducers/rolloutOperation";
import statusSelectionReducer from "./reducers/statusSelection";

const rootReducer = combineReducers({
  rolloutOperation: rolloutOperationReducer,
  statusSelection: statusSelectionReducer,
});
```

StatusSelection.js

```js
import { connect } from "react-redux";
import { selectStatus } from "../../redux/actions/statusSelection";

const mapDispatchToProps = (dispatch) => ({
  onStatusSelected: (status) => dispatch(selectStatus(status)),
});

export default connect(null, mapDispatchToProps)(StatusSelection);
```

actions/rings

```js
// action type
export const SET_RINGS = "SET_RINGS";

// action creator
export const setRings = (data) => ({
  type: SET_RINGS,
  rings: data,
});
```

reducers/rings

```js
import { SET_RINGS } from "../actions/rings";

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
```

RingTable.js

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

const mapStateToProps = (state) => ({
  rings: state.rings.rings,
  selectedStatus: state.statusSelection.status,
  rolloutAction: state.rolloutOperation.action,
});

const mapDispatchToProps = (dispatch) => ({
  setRings: (data) => dispatch(setRings(data)),
});

export default connect(mapStateToProps, mapDispatchToProps)(RingTable);
```

RingTable.js

```js
state = { tableRows: [], allData: [] };

componentDidUpdate(prevProps, prevState) {
  const { rolloutAction: prevAction, selectedStatus: prevStatus } = prevProps;
  const { rolloutAction, selectedStatus } = this.props;
  const { allData } = this.state;

  if (prevAction !== rolloutAction) {
    let modifiedData = allData;

    if (rolloutAction === "start") {
      modifiedData = modifiedData.map((item) => ({
        ...item,
        status: item.status === "Paused" ? "Ongoing" : item.status,
      }));
    } else if (rolloutAction === "pause") {
      modifiedData = modifiedData.map((item) => ({
        ...item,
        status: item.status === "Ongoing" ? "Paused" : item.status,
      }));
    } else if (rolloutAction === "abort") {
      modifiedData = modifiedData.map((item) => ({
        ...item,
        status:
          item.status === "Ongoing" || item.status === "Paused"
            ? "Aborted"
            : item.status,
      }));
    }
    this.setState({ allData: modifiedData });
  }

  if (prevStatus !== selectedStatus || prevState.allData !== allData) {
    const selectedData =
      selectedStatus === "All"
        ? allData
        : allData.filter((data) => data.status === selectedStatus);

    this.setState({ tableRows: selectedData });
  }
}
```

References:

- https://github.com/mavisjheng/create-react-app/commits/master

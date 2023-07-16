<script>
  import { stateStore } from './stores';
  import SomeComponent from './SomeComponent.svelte';

  const initialState = {
    user: null, 
  };

  function reducer(state, action) {
    switch (action.type) {
      case 'SET_USER':
        return { ...state, user: action.user };
      default:
        return state;
    }
  }

  const [state, dispatch] = stateStore(initialState, reducer);
</script>

<stateStore.Provider value={{ state, dispatch }}>
  <SomeComponent />
</stateStore.Provider>

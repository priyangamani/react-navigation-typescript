# react-navigation-typescript

import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";

const Stack = createStackNavigator();
const MockedNavigator = ({ component, params = {} }) => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen
          name="MockedScreen"
          component={ component }
          initialParams={ params }
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default MockedNavigator;


import React from "react";
import MenuButton from "./MenuButton";
import { render, fireEvent } from "@testing-library/react-native";
import { DrawerActions } from "@react-navigation/native";

const mockedDispatch = jest.fn();

// Mocks like this need to be configured at the top level 
// of the test file, they can't be setup inside your tests.
jest.mock("@react-navigation/native", () => {
  const actualNav = jest.requireActual("@react-navigation/native");
  return {
    ...actualNav,
    useNavigation: () => ({
      navigate: jest.fn(),
      dispatch: mockedDispatch,
    }),
  };
});

describe("MenuButton", () => {
  beforeEach(() => {
    // Alternatively, set "clearMocks" in your Jest config to "true"
    mockedDispatch.mockClear();
  });

  it("toggles navigation drawer on press", () => {
    const { getByTestId } = render(<MenuButton />);
    const button = getByTestId("menu-button");
    fireEvent.press(button);

    expect(mockedDispatch).toHaveBeenCalledTimes(1);
    expect(mockedDispatch).toHaveBeenCalledWith(DrawerActions.toggleDrawer());
  });
});

# Discount-Calculator
MAD Assignment 3 by SP18-BCS-109
import * as React from 'react';
import { useState, useEffect } from 'react';
import {
  View,
  Text,
  StyleSheet,
  TextInput,
  Button,
  TouchableOpacity,
  Keyboard,
  Alert,
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import Constants from 'expo-constants';
import { DataTable } from 'react-native-paper';

function HomeScreen({ navigation }) {
  const [getPrice, setPrice] = useState(0);
  const [getPercent, setPercent] = useState(0);
  const [getResult, setResult] = useState(0);
  const [getList, setList] = useState([]);

  useEffect(() => {
    compute();
  });

  navigation.setOptions({
    headerRight: () => (
      <Button
        title="history"
        fontFamily="Roboto"
        letterSpacing="8"
        borderColor="#ffffff"
        onPress={() => {
          navigation.navigate('History', {
            getList: getList,
            setList: setList,
          });
        }}></Button>
    ),
  });

  const addItem = () => {
    setList([
      ...getList,
      {
        key: Math.random().toString(),
        dataPrice: getPrice,
        dataPercent: getPercent,
        dataResult: getResult,
      },
    ]);
    setPrice(0);
    setPercent(0);
    Keyboard.dismiss();
  };

  const compute = () => {
    return setResult(getPrice - getPrice * (getPercent / 100));
  };

  return (
    <View style={styles.container}>
      <Text style={styles.paragraph}>CALCULATE DISCOUNT</Text>
      <TextInput
        placeholder="Enter Price"
        keyboardType="number-pad"
        onChangeText={(getPrice) => setPrice(getPrice)}
        style={{height: 50, 
                borderColor: '#F5F5F5', 
                borderWidth: 3,
                marginTop:10,
                color:'#f5f5f5'
                }}
        value={getPrice}
      />
      <TextInput
        placeholder="Enter Discounted Percentage"
        keyboardType="number-pad"
        onChangeText={(getPercent) => setPercent(getPercent)}
        style={{ height: 50,
                 color:'#ffffff',
                 borderColor: '#F5F5F5', 
                 borderWidth: 3 ,
                 marginTop: 10,
                 color:'#f5f5f5'}}
        value={getPercent}
        maxLength={2}
      />

      <View>
        <View style={styles.fixToText}>
          <Button title="SAVE" 
          fontFamily="Roboto"
          letterSpacing="8"
          borderColor="#ffffff"
          disabled={getPrice == ''} onPress={addItem} />
        </View>

        <View>
          <Text style={{ fontWeight: 'bold',
                         fontFamily:'Roboto',
                         color: '#ffffff',
                         letterSpacing: 3,
                         marginTop:15}}>DISCOUNTED PRICE: {getResult}</Text>
        </View>
      </View>
    </View>
  );
}

function HistoryScreen({ navigation, route }) {
  navigation.setOptions({
    headerRight: () => <Button title="CLEAR" 
                        fontFamily="Roboto"
                        letterSpacing="8"
                        borderColor="#ffffff"
                        onPress={clearAll}></Button>,
  });

  const list = route.params.getList;
  const setl = route.params.setList;
  const [newList, setNewList] = useState(list);

  const removeItem = (itemKey) => {
    let update = list.filter((item) => item.key !== itemKey);
    console.log(update);
    navigation.setParams(setl(update));
    setNewList(update);
    //setNewList(navigation.setParams(setl(list.filter((item) => item.key !== itemKey))))
  };

  const clearAll = () => {
    Alert.alert(
      'Clear All',
      'Are you sure you want to delete history?',
      [
        { text: 'Yes', onPress: () => {
          setl([])
          setNewList([])} },
      ],
      { cancelable: false }
    )
  };

  return (
    <View>
      <DataTable>
        <DataTable.Header>
          <DataTable.Title numeric  
                          style={{flex: 3, fontFamily: 'Roboto',letterSpacing:0.5}}>Original                            Price</DataTable.Title>
          <DataTable.Title numeric 
                          style={{flex: 3, fontFamily: 'Roboto',letterSpacing:0.5}}>Discount</                          DataTable.Title>
          <DataTable.Title numeric  style={{flex: 3, fontFamily: 'Roboto',letterSpacing:0.5}}                           >Final Price</DataTable.Title>
          <DataTable.Title></DataTable.Title>
        </DataTable.Header>
      </DataTable>

      {newList.map((item, index) => (
        <DataTable>
          <DataTable.Row>
            <DataTable.Cell  style={{flex: 1}} numeric>{item.dataPrice}</DataTable.Cell>
            <DataTable.Cell  style={{flex: 1}}numeric>{item.dataPercent}</DataTable.Cell>
            <DataTable.Cell  style={{flex: 1}}numeric>{item.dataResult}</DataTable.Cell>
            <DataTable.Cell numeric>
              <TouchableOpacity onPress={() => removeItem(item.key)}>
                <View
                  style={{
                    backgroundColor: '#4f7ea1',
                    borderRadius: 45,
                    borderColor:'black',
                    padding: 3,
                    justifyContent: 'center',
                    width: 40,
                    alignItems: 'center',
                  }}>
                  <Text style={styles.cross}>X</Text>
                </View>
              </TouchableOpacity>
            </DataTable.Cell>
          </DataTable.Row>
        </DataTable>
      ))}
    </View>
  );
}

const Stack = createStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName={'HOME'}
        screenOptions={{
          headerTitleAlign: 'center',
          headerTintColor: '#ffffff',
          headerStyle: {
            backgroundColor: '#4f7ea1',
            fontFamily:'Roboto'
          },
        }}>
        <Stack.Screen name="HOME" component={HomeScreen} />
        <Stack.Screen name="History" component={HistoryScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 10,
    backgroundColor: '#8cabc3',
    padding: 10,
  },
  paragraph: {
    margin: 24,
    fontFamily: 'Roboto',
    fontSize: 20,
    fontWeight: 'bold',
    letterSpacing: 2,
    textAlign: 'center',
    color:'#ffffff'
  },

  fixToText: {
    flexDirection: 'row',
    justifyContent: 'center',
    marginTop: 25,
    borderColor:'black'
  },
  cross: {
    color: 'black',
  },
});
export default App;

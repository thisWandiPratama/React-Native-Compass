# React-Native-Compass
Example Campass With React Native

# Install Package 
- [react-native-sensors](https://react-native-sensors.github.io/docs/Installation.html)
- [react-native-easy-grid](https://github.com/GeekyAnts/react-native-easy-grid)

# Screenshot
![arahkiblat](https://github.com/thisWandiPratama/React-Native-Compass/blob/master/arah%20kiblat?raw=true)

# Code
```
import React, {Component} from "react";
import {Text, View, Image, Dimensions, StyleSheet} from "react-native";
import {Grid, Col, Row} from "react-native-easy-grid";
import {magnetometer, SensorTypes, setUpdateIntervalForType} from "react-native-sensors";

const {height, width} = Dimensions.get("window");

export default class Kompas extends Component {
  constructor() {
    super();
    this.state = {
      magnetometer: "0",
    };
  }

  componentDidMount() {
    this._toggle();
  }

  componentWillUnmount() {
    this._unsubscribe();
  }

  _toggle = () => {
    if (this._subscription) {
      this._unsubscribe();
    } else {
      this._subscribe();
    }
  };

  _subscribe = async () => {
    setUpdateIntervalForType(SensorTypes.magnetometer, 100);
    this._subscription = magnetometer.subscribe(
      sensorData => this.setState({magnetometer: this._angle(sensorData)}),
      error => console.log("The sensor is not available"),
    );
  };

  _unsubscribe = () => {
    this._subscription && this._subscription.unsubscribe();
    this._subscription = null;
  };

  _angle = magnetometer => {
    let angle = 0;
    if (magnetometer) {
      let {x, y} = magnetometer;
      if (Math.atan2(y, x) >= 0) {
        angle = Math.atan2(y, x) * (180 / Math.PI);
      } else {
        angle = (Math.atan2(y, x) + 2 * Math.PI) * (180 / Math.PI);
      }
    }
    return Math.round(angle);
  };

  _direction = degree => {
    if (degree >= 22.5 && degree < 67.5) {
      return "NE";
    } else if (degree >= 67.5 && degree < 112.5) {
      return "E";
    } else if (degree >= 112.5 && degree < 157.5) {
      return "SE";
    } else if (degree >= 157.5 && degree < 202.5) {
      return "S";
    } else if (degree >= 202.5 && degree < 247.5) {
      return "SW";
    } else if (degree >= 247.5 && degree < 292.5) {
      return "W";
    } else if (degree >= 292.5 && degree < 337.5) {
      return "NW";
    } else {
      return "N";
    }
  };

    _degree = magnetometer => {
    return magnetometer - 90 >= 0
      ? magnetometer - 90
      : magnetometer + 271;
  };

  render() {
    return (
      <Grid>
        <Row style={{alignItems: "center"}} size={2}>
          <Text
            style={{
              color: "#fff",
              fontSize: height / 27,
              width: width,
              position: "absolute",
              textAlign: "center",
            }}
          >
            {this._degree(this.state.magnetometer)}°
          </Text>

          <Col style={{alignItems: "center"}}>
                <Image
                source={require("../../../assets/kompas.png")}
                style={{
                    marginRight:20,
                    height: 165,
                    justifyContent: "center",
                    alignItems: "center",
                    resizeMode: "contain",
                    transform: [
                    {rotate: 360 - this.state.magnetometer + "deg"},
                    ],
                }}
                />
          </Col>
        </Row>
      </Grid>
    );
  }
}
```

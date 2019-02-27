---
name: React native Flatlist Issue
about: I am facing an issue while setting Flatlist numColumn >=2, throwing a violation
  error
title: Flatlist numColumn React native
labels: bug
assignees: ''

---

"axios": "^0.18.0",
 "react": "16.6.3",
 "react-native": "^0.58.4",
 "react-native-elements": "^1.0.0",
 "react-native-gesture-handler": "^1.0.15",
 "react-native-vector-icons": "^6.2.0",
 "react-navigation": "^3.1.2",
When set numColumn = {1} it runs perferctly fine, above value 1 it throws error

<FlatList data={this.state.mdata}
              renderItem={this.renderList}
              keyExtractor={(item, index) => `${index}`}
              horizontal={false} numColumns={2}/>
ImageList.js

import {Text, View, ScrollView, TouchableOpacity,StyleSheet,Dimensions,FlatList} from 'react-native';
import  {Card}from 'react-native-elements';
import API from './axios';
const styles = StyleSheet.create({
    image: {
        flex:1,width:'50%',height:'50%',
        alignItems: 'center',
        justifyContent: 'center',
    },

})

export default class ImageList extends React.Component {

    constructor(props) {
        super(props);
        this.state = {
            mloading: false,
            mdata: [],
            current_page: 1,
            merror: null,
            mhasMore: true
        }
    }
    componentDidMount()  { this.getListOfPictures(); }

    render() {
        return (

            <FlatList
              data={this.state.mdata}
              renderItem={this.renderList}
              keyExtractor={(item, index) => `${index}`}
              horizontal={false} numColumns={2}/> 

           )
    }


    /********************************************************************************
     * getListOfPictures All pictures for dashboard
     ********************************************************************************/
    getListOfPictures = () => {
        if (this.state.mloading) { return; }
        this.setState({ mloading: true });

        API.get(`dashboard/all`)
            .then(res => {
                console.log("reco1m",res.data.recommendations[0]);
                const nextPictures= res.data.recommendations.map(data => ({
                    title: data.style,
                    image_url: data.img,
                    description: data.description,
                    id: data.style
                }));
                console.log(nextPictures);
                this.setState({
                    mhasMore: true,
                    mdata: [...this.state.mdata, ...nextPictures],
                    mloading: false,
                    current_page: this.state.current_page + 1})
            }).catch(error => {this.setState({ merror:error, mloading: false });});
    }

    isCloseToBottom({ layoutMeasurement, contentOffset, contentSize }) {
        return layoutMeasurement.height + contentOffset.y
        >= contentSize.height - 0;
    }

    renderList = () => {
        return ( this.state.mdata.map((u) => {
            return (
                <View style={{flex: 0.5, height: 150, margin: 5}}>
                <TouchableOpacity
                    key={u.id} >
                   <Card
                       featuredTitle={u.title}
                       image={{ uri: u.image_url }}
                       imageStyle={styles.image}>
                       <View style={{ padding: 10 }}>
                           <Text style={{ fontSize: 15}}>Description:</Text>
                           <Text>{u.description}</Text>
                       </View>
                   </Card>
                </TouchableOpacity>
                </View>
            );
        }))
    }


}

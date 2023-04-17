# tugas_individu4_pam
## Nama  : Sarlini
## NIM   : 120140089
## Kelas : PAM RB

Dalam aplikasi pemesanan tiket pesawat, Redux Thunk dapat digunakan untuk mengelola kode asynchronous seperti memanggil API untuk memuat daftar penerbangan atau memesan tiket. Contoh aksi Redux Thunk yang asinkron dapat ditulis seperti ini:

export const fetchFlights = () => {
  return (dispatch) => {
    dispatch(fetchFlightsRequest());
    return api.get('/flights')
      .then(response => {
        const flights = response.data;
        dispatch(fetchFlightsSuccess(flights));
      })
      .catch(error => {
        dispatch(fetchFlightsFailure(error.message));
      });
  };
};

 Dalam Redux Saga dapat digunakan untuk mengelola kode asynchronous dengan menggunakan generator functions dan konsep effect. Contoh aksi Redux Saga yang asinkron dapat ditulis seperti ini:
 
function* fetchFlights() {
  try {
    const response = yield call(api.get, '/flights');
    const flights = response.data;
    yield put(fetchFlightsSuccess(flights));
  } catch (error) {
    yield put(fetchFlightsFailure(error.message));
  }
}

export function* watchFetchFlights() {
  yield takeLatest(FETCH_FLIGHTS_REQUEST, fetchFlights);
}

Dalam hal readability, Redux Saga dapat dianggap lebih mudah dibaca karena menggunakan generator functions yang membuat alur kode menjadi lebih linear. Namun, Redux Thunk dapat lebih mudah dipahami oleh developer yang belum terbiasa dengan konsep generator functions.Dalam hal maintainability, Redux Saga dapat dianggap lebih mudah untuk dipelihara karena konsep effect memungkinkan untuk mengisolasi kode asinkron dari kode synchronous. Namun, Redux Thunk dapat lebih mudah dipelihara jika aksi Redux yang dilakukan hanya sederhana dan tidak memerlukan banyak manipulasi state.
Dalam hal testability, keduanya sama-sama dapat diuji dengan menggunakan Jest dan Enzyme. Namun, Redux Saga memudahkan pengujian kode asinkron karena menggunakan generator functions yang dapat di-pause dan di-resume selama pengujian. Sedangkan pada Redux Thunk, perlu melakukan mocking terhadap dispatch dan API yang digunakan.
Contoh tes unit untuk menguji aksi Redux Thunk pada aplikasi pemesanan tiket pesawat dapat ditulis seperti ini:

import configureMockStore from 'redux-mock-store';
import thunk from 'redux-thunk';
import * as actions from './actions';
import * as types from './types';
import api from '../../api';

const middlewares = [thunk];
const mockStore = configureMockStore(middlewares);

describe('fetchFlights', () => {
  it('creates FETCH_FLIGHTS_SUCCESS when fetching flights has been done', () => {
    const flights = [{ id: 1, name: 'Flight 1' }];
    const expectedActions = [
      { type: types.FETCH_FLIGHTS_REQUEST },
      { type: types.FETCH_FLIGHTS_SUCCESS, flights },
    ];
    api.get = jest.fn(() => Promise.resolve({ data: flights }));

    const store = mockStore({ flights: [] });

    return store.dispatch(actions.fetchFlights()).then(() => {
      expect(store.getActions()).toEqual(expectedActions

# node-alsong
An alsong lyric finder for node.

## Notice
**From the `alsong@^3.0.0`, most of the API have been changed and may not work with your old code**  
Please see the Migration section for more information

## Usage
### Lyric Examples
An example for the `Lyric` object is denoted by following:
```js
{
	lyricId: 7241381,
	title: 'STEP',
	artist: 'ClariS',
	album: 'STEP',
	lyric: {
		'0': [ '' ],
		'14400': [ '君に謳い　聴かせたいこと', '키미니우타이 키카세타이코토', '네게 간절하게 들려주고 싶은 말' ],
		'21180':
			[ '壊れないように大事に　しすぎちゃってさ',
			'코와레나이요우니 다이지니 시스기챳테사',
			'부서지지 않도록 소중히 간직만 하고 있어' ],
		'28180': [ '今日もメールを　綴ってはまた', '쿄우모 메ー루오 츠즛테와 마타', '오늘도 메일을 쌓아두고서는 또' ],
		'34300': [ '空白で塗り替えて千切ってしまうの', '쿠우하쿠데 누리카에테 치깃테시마우노', '공백으로 다시 칠해 지워버려' ],
		(...)
	},
	lyricRaw: '[00:14.40]君に謳い　聴かせたいこと<br>(...)',
	firstRegister: { name: '오재령', email: 'xxxxxx@naver.com', url: '', phone: 'xxx-xxxx-xxxx', comment: '노래가사는 찾아 (...)' },
	register: { name: '디시애니일본', email: '', url: '', phone: '', comment: '' }
}
```

----

### alsong(artist, title [, option ])
Finds a lyric by artist and title.  
Returns a promise which resolves array of lyric items.

#### Arguments
* **artist**: `string`  
  Aritst name of the song
* **title**: `string`  
  Title of the song
* **option**: `object?`  
  * **playtime**: `number`
    The length of the track. (unit: Millisecond)
  * **page**: `number`  
    The desired page of result, starting from zero

#### Returns
```
Promise<{
	lyricId: number,
	playtime: number,
	title: string,
	artist: string,
	album: string,
	registerDate: Date
}[]>
```  
**You can get the corresponding lyric using `alsong.getLyricById(item.lyricId)`**

#### Example
```js
const alsong = require('alsong');
alsong('ClariS', 'irony', { page: 0, playtime: 267911 })
	.then((v) => {
		console.log(v[0]);
		/* {
			lyricId: 5407588,
			playtime: 267911,
			artist: 'Claris - Irony',
			album: 'Claris - Irony',
			title: 'Claris - Irony',
			registerDate: 2010-12-13T14:15:26.000Z
		} */

		return alsong.getLyricById(v[0].lyricId);
	})
	.then(console.log));
```

----

### alsong(music)
Finds a lyric by stream, buffer, or string.  
Returns a promise which resolves lyrics.

#### Arguments
* **music**: `stream | Buffer | string`  
  The Node.js Buffer, stream, or file path of the music file.

#### Returns
`Promise<Lyric>` for existing lyric  
`Promise<null>` for non-existing lyric

#### Example
```js
const lyric1 = await alsong('./only my railgun.mp3');
const lyric2 = await alsong(fs.readFileSync('./kimi_no_shiranai_monogatari.mp3'));
const lyric3 = await alsong(fs.createReadStream('./キズナミュージック.mp3'));
```

----

### alsong.getHash(music)
Gets the lyric hash of given music by a buffer, stream, or a file.  
Returns a promise which resolves the hash.

#### Arguments
* **music**: `stream | Buffer | string`  
  The Node.js Buffer, stream, or file path of the music file.

#### Returns
`Promise<string>`: The lyric hash of the music file.  
**You can get the corresponding lyric using `alsong.getLyricByHash(hash)`**

#### Example
```js
console.log(await alsong.getHash('./Bassline yatteru.mp3'));
// 123456789abcdef123456789abcdef12
```

----

### alsong.getLyric(music)
An alias for `alsong(music)`

----

### alsong.getLyricById(id)
Finds the lyric by lyricId.  
Returns a promise which resolves lyrics.

#### Arguments
* **id**: `string | number`  
  The id of the lyric

#### Returns
`Promise<Lyric>` for existing lyric  
`Promise<null>` for non-existing lyric

#### Example
```js
await alsong.getLyricById(9207318);
```

----

### alsong.getLyricByHash(hash)
Finds the lyric by a hash of a music.  
Returns a promise which resolves lyrics.

#### Arguments
* **hash**: `string`  
  The hash generated by md5-ing the first 163840 bytes, except the headers, from the music file.

#### Returns
`Promise<Lyric>` for existing lyric  
`Promise<null>` for non-existing lyric

#### Example
```js
await alsong.getLyricById('123456789abcdef123456789abcdef12');
```

----

### alsong.getLyricListByArtistName(artist, title [, options ])
An alias for `alsong(artist, title, options)`

## Migration
The API of `alsong@3` have been drastically changed from `alsong@2`.  
You can migrate from 2 to 3 by using the `alsong.compat`:
> **NOTE**: The `skipParse` option is not supported anymore.

### alsong.compat([, resolver ])
Returns API interface for `alsong@1` and `alsong@2`.

#### Arguments
* **resolver**: `Optional<'v1' | 'v2'>`  
  If the resolver is `v1`, it will use the old Alsong API endpoint.  
  Defaults to `v2`, which uses the new Alsong API endpoint, but translates the returned lyric object.

  **WARNING** If you use the `'v2'` resolver, it will make N + 1 API requests for `alsong(artist, title[, options ])`.

#### Examples
```js
const alsongCompat = alsong.compat();
const lyric = await alsongCompat('./hunch gray.mp3')
// { strInfoId: '7241381', strOnlyLyricWord: '0', ... }
```


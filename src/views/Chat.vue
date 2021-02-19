<template>
	<chat-window
		:current-user-id="currentUserId"
		:room-id="roomId"
		:rooms="rooms"
		:messages="messages"
		:rooms-loaded="true"
		:messages-loaded="messagesLoaded"
		@fetch-messages="fetchMessages"
		@send-message="sendMessage"
		@add-room="addRoom"
	/>
</template>

<script>
import ChatWindow from 'vue-advanced-chat'
import 'vue-advanced-chat/dist/vue-advanced-chat.css'
import { rooms } from '../data/rooms'
import { messages } from '../data/messages'

export default {
	components: {
		ChatWindow,
	},
	data() {
		return {
			roomId: 'room_1', 
			rooms: rooms,
			messages: [],
			currentUserId: 1234,
			messagesLoaded: false,
        }
        
	},
	mounted() {},
	methods: {
		fetchMessages({ room, options }) {
			//options.reset when no room is clicked
			if (options.reset) {
				this.messagesLoaded = false
				this.roomId = room.roomId
			}

			//use timeout to initiate async server fetched data
			setTimeout(() => {
				this.messages = messages[this.roomId]
				this.messagesLoaded = true
			}, 0)
		},
		sendMessage(params) {
			const date =
				new Date().getDate() +
				' ' +
				new Date().toLocaleString('default', { month: 'long' }) +
				' ' +
				new Date().getFullYear()

			const timestamp = new Date().toLocaleString('default', {
				hour: 'numeric',
				minute: 'numeric',
				hour12: false,
			})

			messages[this.roomId].push({
				_id: new Date().getTime(),
				content: params.content,
				senderId: this.currentUserId,
				date: date,
				timestamp: timestamp,
				distributed: true,
			})
			console.log(params.content)

			const roomIndex = rooms.findIndex((room) => room.roomId === this.roomId)

			rooms[roomIndex].lastMessage = {
				content: params.content,
				senderId: 1234,
				username: 'John Doe',
				timestamp: timestamp,
			}
		},
		addRoom() {
			const roomId = 'room_' + new Date().getTime()

			rooms.push({
				roomId: roomId,
				roomName: roomId,
				//avatar: 'https://i.pravatar.cc/30',
				users: [
					{
						_id: 1234,
						username: 'John Doe',
						//avatar: 'https://i.pravatar.cc/30',
					},
					{
						_id: 4321,
						username: 'John Snow',
						//avatar: 'https://i.pravatar.cc/30',
					},
				],
			})
			//messages for new room creation
			messages[roomId] = []

			this.roomId = roomId
			this.messages = []
		},
	},
}
</script>

<style></style>

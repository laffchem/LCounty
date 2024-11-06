<template>
	<div>
		<h1>Work Orders</h1>
		<button
			@click="fetchTechnicians"
			type="button"
			class="btn btn-primary"
			data-bs-toggle="modal"
			data-bs-target="#submitModal"
		>
			Create Work Order
		</button>

		<Modal :technicians="technicians" @workOrdersUpdated="updateWorkOrders" />

		<div class="container mt-5">
			<label class="mr-2">Status:</label>
			<select
				class="form-select"
				v-model="status"
				@change="fetchWorkOrdersByStatus"
			>
				<option value="Open">Open</option>
				<option value="Closed">Closed</option>
			</select>
		</div>
		<div class="container">
			<table class="table">
				<thead>
					<tr>
						<!-- <th>ID</th> -->
						<th scope="col">Email</th>
						<th scope="col">Contact Name</th>
						<th scope="col">Contact Number</th>
						<th scope="col">Problem</th>
						<th scope="col">Date Received</th>
						<th scope="col">Status</th>
						<th scope="col">Technician</th>
						<th scope="col">Remove</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="order in workOrders" :key="order.id">
						<!-- <td>{{ order.id }}</td> -->
						<td>{{ order.email }}</td>
						<td>{{ order.contactName }}</td>
						<td>{{ order.contactNumber }}</td>
						<td>{{ order.problem }}</td>
						<td>{{ order.dateReceived }}</td>
						<td>
							<select v-model="order.status" @change="updateStatus(order)">
								<option value="Open">Open</option>
								<option value="Closed">Closed</option>
							</select>
						</td>
						<td>{{ order.technician.firstName }}</td>
						<td>
							<button
								type="button"
								class="btn-close"
								@click="deleteWorkOrder(order)"
							></button>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
	</div>
</template>

<style scoped></style>

<script>
import Modal from './components/Modal.vue';
export default {
	props: ['technicians'],
	data() {
		return {
			workOrders: [],
			status: 'Open',
			showModal: false,
			technicians: [],
		};
	},
	components: {
		Modal,
	},
	mounted() {
		this.fetchWorkOrdersByStatus();
	},
	methods: {
		fetchWorkOrdersByStatus() {
			this.$axios
				.get(`/api/workorders/status/${this.status}`)
				.then((response) => {
					console.log(response);
					this.workOrders = response.data;
				})
				.catch((err) => console.error(err));
		},
		fetchTechnicians() {
			this.$axios
				.get('/api/technician')
				.then((response) => {
					this.technicians = response.data;
				})
				.catch((error) => {
					console.error('Error fetching technicians:', error);
				});
		},
		updateStatus(order) {
			this.$axios
				.patch(`/api/workorders/${order.id}`, {
					status: order.status,
				})
				.then((response) => {
					console.log('Status updated:', response.data);
					this.fetchWorkOrdersByStatus();
				})
				.catch((error) => {
					console.error('Error updating status:', error);
				});
		},
		updateWorkOrders(newWorkOrders) {
			this.workOrders = newWorkOrders;
		},
		deleteWorkOrder(order) {
			this.$axios.delete(`/api/workorders/${order.id}`).then((response) => {
				console.log('Work order deleted:', response.data);
				this.fetchWorkOrdersByStatus();
			});
		},
	},
};
</script>
